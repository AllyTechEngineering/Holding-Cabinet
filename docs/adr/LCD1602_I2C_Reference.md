# LCD1602 I2C Reference

## Component Identification

- **Display:** SunFounder LCD1602 (16x2 character LCD)
- **LCD controller:** HD44780U (or compatible), COB-mounted on the LCD glass — not a separately identifiable part number, confirmed via Hitachi HD44780U datasheet instruction set match
- **I2C backpack:** PCF8574 8-bit I2C I/O expander (TI PCF8574, TSSOP/SOIC variants common on these backpacks)
- **I2C address:** 0x27 (default, A0-A2 pins unstrapped) — confirm via I2C bus scan once board is in hand; alternate common default is 0x3F depending on backpack vendor

## PCF8574 Bit Mapping

This is the wiring on the specific I2C LCD adapter board in use (per `I2C_LCD_Adapter_Schematic.pdf`):

| PCF8574 Bit | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|---|---|---|---|---|---|---|---|---|
| Signal | DB7 | DB6 | DB5 | DB4 | BL | EN | R/W | RS |

- **RS** (bit 0): 0 = instruction register, 1 = data register
- **R/W** (bit 1): 0 = write, 1 = read (tie low / always write in this design — no read-back)
- **EN** (bit 2): enable strobe, pulse high then low to latch each nibble
- **BL** (bit 3): backlight control (via Q1 transistor on adapter board)
- **DB4-DB7** (bits 4-7): upper nibble — this is a 4-bit interface; each 8-bit LCD command is sent as two nibble writes, high nibble first

Each nibble write to the LCD = two I2C byte writes to the PCF8574: EN high, then EN low, with the data nibble and RS/R/W held stable across both.

## Initialization Sequence (4-bit mode)

Per HD44780U datasheet, "Initializing by Instruction," Figure 24 (4-bit interface path):

| Step | RS,R/W | DB7-DB4 | Value | Wait After | Purpose |
|---|---|---|---|---|---|
| 1 | 0,0 | 0011 | 0x3 | >4.1 ms | Function set (forces known state, sent as nibble) |
| 2 | 0,0 | 0011 | 0x3 | >100 µs | Function set (repeat) |
| 3 | 0,0 | 0011 | 0x3 | 37 µs | Function set (repeat, per spec) |
| 4 | 0,0 | 0010 | 0x2 | 37 µs | Function set → switch to 4-bit interface |
| 5 | 0,0 | full byte | 0x28 | 37 µs | Function set: DL=0 (4-bit), N=1 (2 lines), F=0 (5x8 font) |
| 6 | 0,0 | full byte | 0x08 | 37 µs | Display off |
| 7 | 0,0 | full byte | 0x01 | 1.52 ms | Display clear |
| 8 | 0,0 | full byte | 0x06 | 37 µs | Entry mode set: I/D=1 (increment), S=0 |
| 9 | 0,0 | full byte | 0x0F | 37 µs | Display on, cursor on, blink on (use 0x0C for display-only, no cursor) |

**Steps 1-3 are nibble-only sends** (high nibble = 0011, no low-nibble write, no RS). This is the step most likely to be gotten wrong — everything from step 4 onward sends full 8-bit commands as two nibble writes each; steps 1-3 send a single nibble with no second write.

## Instruction Subset (commands used post-init)

| Instruction | RS | Byte | Description |
|---|---|---|---|
| Clear display | 0 | 0x01 | Clears DDRAM, resets cursor to address 0 |
| Return home | 0 | 0x02 | Resets cursor/display position, DDRAM unchanged |
| Entry mode set | 0 | 0x04-0x07 | Cursor direction / display shift on write |
| Display on/off control | 0 | 0x08-0x0F | Display, cursor, blink on/off bits |
| Set DDRAM address | 0 | 0x80 \| addr | Position cursor for next character write (line 1 starts 0x00, line 2 starts 0x40) |
| Write data to DDRAM | 1 | character code | Writes a character at current cursor position |

## Notes

- Full HD44780U instruction set and timing diagrams: Hitachi/manufacturer datasheet (not redistributed here — see manufacturer source)
- PCF8574 electrical specs and I2C timing: TI PCF8574 datasheet (ti.com)
- No read-back (busy flag polling) implemented in this design — R/W tied low, fixed delays used instead per the timing in the table above