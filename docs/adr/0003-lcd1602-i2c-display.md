# ADR-0003: LCD1602 I2C Display Over 4-Digit 7-Segment

## Status

Accepted — supersedes ADR-0002 (NPN segment drivers)

## Context

The initial UI design used a common-anode CA56-12EWA 4-digit 7-segment display, driven via P-channel MOSFETs (digit select, high-side) and NPN transistors (segment drive, low-side), per ADR-0002. This required 12 GPIO pins (PC0-PC7 for segments, PB12-PB15 for digit select), a dedicated real-time FreeRTOS task (MuxTask) synchronized to a TIM6 ISR for display multiplexing, and a custom driver (`seg_board.c`) handling active-low/active-high polarity inversion across the two transistor types.

A SunFounder LCD1602 with I2C (PCF8574) backpack was evaluated as an alternative, using hardware already on hand.

## Decision

Replace the 4-digit 7-segment display with an LCD1602 character display over I2C for the product line's UI (Models 1-3).

## Rationale

- **Cost:** LCD1602 + I2C backpack module priced comparably to or below the 7-segment display alone, with no additional MOSFET/transistor BOM cost
- **GPIO:** Reduces display interface from 12 GPIO pins to 2 (shared I2C2 bus, alongside the BME280 sensor — different I2C addresses, no bus conflict)
- **Firmware simplicity:** Eliminates the dedicated real-time multiplexing task and ISR-driven semaphore handoff; display updates become a low-frequency I2C write, compatible with normal-priority task scheduling
- **UI capability:** Character display supports labeled units and status text (e.g., "PROOF 82F"), not achievable on 4-digit numeric 7-segment

## Consequences

- `seg_board.c` and associated polarity-inversion logic are removed
- MuxTask and its TIM6 ISR binding are removed from the FreeRTOS task architecture; display updates move to a lower-priority periodic task
- PC0-PC7 and PB12-PB15 are freed for other use
- KiCad Display sub-sheet is redesigned: MOSFET/transistor drive circuitry removed, replaced with I2C header/connector to the LCD backpack
- BOM.md updated to remove FET, NPN transistor, and CA56-12EWA line items; add LCD1602 + PCF8574 module
- Housing/enclosure design (shared across Models 1-3) must accommodate the LCD1602 module footprint (~80x36mm), which differs from the 4-digit 7-segment footprint — physical fit to be confirmed against enclosure design when hardware is in hand
- New driver work required: `lcd1602.c` implementing the HD44780U-over-PCF8574 command sequence (see `docs/LCD1602_I2C_Reference.md`)