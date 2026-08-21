# Architecture — Holding Cabinet (Proofer)

System-level view of the firmware and hardware architecture: how the
system is built, not what it does (see PRD.md) or why specific decisions
were made (see docs/adr/).

---

## 1. System Overview

Single-MCU embedded system on a shared PCBA and firmware codebase,
configured per product model at build time.

**Development platform:** NUCLEO-L476RG (STM32L476RG)
**Production target:** STM32C031C6
**RTOS:** FreeRTOS / CMSIS-RTOS v2
**Companion Wi-Fi/BLE module (Models 3/4):** ESP32-C6-MINI-1-N4 (see ADR-0004)

The STM32 is the real-time control brain for all models. The companion
module owns the radio stack entirely, communicating to the STM32 over
UART.

---

## 2. Peripheral / Bus Summary

| Peripheral | Interface | Pins | Notes |
|---|---|---|---|
| BME280 (temp/humidity) | I2C2 | PB10 (SCL) / PB11 (SDA) | Addr 0x76 |
| LCD1602 (via PCF8574) | I2C2 | PB10 (SCL) / PB11 (SDA) | Addr 0x27, shared bus, no conflict |
| Switches 1–4 | GPIO | PB4–PB7 | SW1=Up, SW2=Down, SW3=Mode, SW4=TBD; PB4 remapped off NJTRST |
| Indicator LEDs 1–3 | GPIO | PA8–PA10 | |
| Heater relay | GPIO | PC9 | NPN low-side switch, flyback diode, pull-down base resistor |
| Wi-Fi/BLE module | UART | TBD | ESP32-C6-MINI-1-N4; pin assignment/baud/framing TBD |

---

## 3. Interfaces

### 3.1 I2C2 Bus (Shared)
STM32 as master. See docs/reference/LCD1602_I2C_Reference.md for PCF8574
bit mapping and HD44780U init sequence.

### 3.2 STM32 ↔ Wi-Fi/BLE Module (UART)
**Status:** Module selected (ESP32-C6-MINI-1-N4, see ADR-0004). Pin
assignment, baud rate, framing: TBD.

| Command | Direction | Purpose | Frame/opcode |
|---|---|---|---|
| Reprovision Wi-Fi | STM32 → module | Triggered from Setup Menu (Section 4). Maps to `wifi_prov_mgr_reset_provisioning()` + re-entry into BLE advertising. | TBD |
| Reprovision Wi-Fi (response) | Module → STM32 | Acknowledge/report result so LCD can reflect status. | TBD |

### 3.3 BLE ↔ Companion App (Models 3/4)
**Status:** TBD. Constraint: module's BLE stack used unmodified — no
custom GATT service, no custom BLE application code (see RISK-001).

---

## 4. Setup Menu Entry

Entered by holding SW1 (Up) + SW2 (Down) for 3–5 seconds at power-on.
Button state sampled early in boot, before normal UI task starts.

First defined action: **Reprovision Wi-Fi** — clears stored network
credentials via `wifi_prov_mgr_reset_provisioning()`, device re-enters
BLE advertising for a new provisioning session. Remaining menu structure: TBD.

---

## 5. Data Flow (Control Loop, High-Level)

1. Sensing: BME280 read via I2C2
2. Control: compare sensed temperature against setpoint; drive heater
   relay (PC9) — on/off or duty-cycled
3. Display: current status/setpoint written to LCD1602 via PCF8574
4. Input: switch presses (PB4–PB7) update setpoint/mode. Exception: a
   held Up+Down combo for 3–5s at power-on diverts to the Setup Menu
   (Section 4) instead.
5. (Models 3/4) Connectivity: setpoint/status exchanged with companion
   app via BLE, through the module over UART

Task-to-task communication mechanism (queues, semaphores, shared state +
mutex): TBD.

---

## 6. FreeRTOS Task Architecture

**Status: TBD — fill in from current firmware state.**

| Task | Priority | Responsibility | Status |
|---|---|---|---|
| SensingTask | TBD | Poll BME280 | TBD |
| ControlTask | TBD | Heater relay control loop | TBD |
| DisplayTask | TBD | LCD1602 update | TBD |
| ConnectivityTask | TBD | UART comms w/ module (Models 3/4) | TBD |
| SetupMenuTask | TBD | Button-combo detection, menu navigation | TBD |

---

## 7. Build Configuration Strategy

Single firmware project, per-model build configs, enabled by FreeRTOS
extensibility.

| Model | Config differences vs. base |
|---|---|
| 1 | Temp control only |
| 2 | + Humidity control |
| 3 | + Connectivity (Wi-Fi/BLE, Flutter app) |
| 4 | Same as 3, pro-consumer housing — UI TBD |

---

## 8. Revision Notes

Living document. Sections marked TBD get filled in as the design
solidifies — no speculative backfill.