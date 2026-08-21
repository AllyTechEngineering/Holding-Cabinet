# Interface Control Document (ICD)

## 1. I2C2 Bus (Shared)

Shared bus, STM32 as master. Pins: PB10 (SCL) / PB11 (SDA).

| Device | Address | Notes |
|---|---|---|
| BME280 | 0x76 | SDO tied to GND |
| PCF8574 (LCD1602 backpack) | 0x27 | A0–A2 tied to VCC via pull-ups |

No address conflicts on this bus (confirmed).

### 1.1 BME280 Register Interface — not yet written (stub)
### 1.2 PCF8574 / LCD1602 — see docs/reference/LCD1602_I2C_Reference.md
Bit mapping: P0=RS, P1=R/W, P2=EN, P3=BL, P4–P7=DB4–DB7.

---

## 2. GPIO Interfaces

| Signal | Pins | Notes |
|---|---|---|
| Switches 1–4 | PB4–PB7 | PB4 remapped off default NJTRST |
| Indicator LEDs 1–3 | PA8–PA10 | |
| Heater relay | PC9 | NPN low-side switch, flyback diode, pull-down base resistor |

---

## 3. STM32 ↔ Wi-Fi/BLE Module (UART)

**Status: Module selected (ESP32-C6-MINI-1-N4). Interface details TBD.**

Companion Wi-Fi/BLE module owns the radio stack; STM32C031 remains the
real-time control brain and communicates over UART.

**Open items:**
- UART pin assignment (TBD)
- Baud rate / framing (TBD)
- Message framing protocol (TBD — e.g., length-prefixed, delimiter-based, or vendor AT-command set)
- Error/timeout handling strategy (TBD)

**Command/response set (partial — commands identified so far):**

| Command | Direction | Purpose | Frame/opcode |
|---|---|---|---|
| Reprovision Wi-Fi | STM32C031 → module | Triggered from Setup Menu (see ARCHITECTURE.md Section 4). Maps to `wifi_prov_mgr_reset_provisioning()` + re-entry into BLE advertising. | TBD |

Additional commands (status query, connection state reporting to drive
LCD updates, etc.) to be added as they're identified — this table is not
expected to be complete until firmware implementation.

---

## 4. BLE ↔ Companion App (Models 3/4)

**Status: TBD — pending GATT profile design.**

Open items: GATT service/characteristic UUIDs, provisioning payload
format, post-provisioning data exchange, security/pairing method.

Constraint: module's BLE stack must be used unmodified (see COMPLIANCE.md).

---

## 5. Revision Notes

Living document — update as interfaces are defined, don't backfill TBD
sections speculatively.