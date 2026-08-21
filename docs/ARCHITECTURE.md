## 1. System Overview

The Holding Cabinet is a single-MCU embedded system built on a shared
PCBA and firmware codebase, configured per product model at build time.

**Development platform:** NUCLEO-L476RG (STM32L476RG)
**Production target:** STM32C031C6
**RTOS:** FreeRTOS / CMSIS-RTOS v2

The MCU is the real-time control brain for all models. Models 3/4 add a
companion Wi-Fi/BLE module (radio stack owned entirely by that module,
communicating to the STM32 over UART — see ICD.md).

---

## 2. Hardware Block Diagram (text-based)

[STM32 block: Switches 1-4 → PB4-PB7; PC9 → Heater Relay (AC hot leg,
high-side switch); I2C2 PB10/PB11 → BME280 (0x76) and PCF8574→LCD1602
(0x27); Indicator LEDs → PA8-PA10; UART (TBD pins) → Wi-Fi/BLE Module
(Models 3/4 only)]

---

## 3. Peripheral / Bus Summary

| Peripheral | Interface | Pins | Notes |
|---|---|---|---|
| BME280 (temp/humidity) | I2C2 | PB10 (SCL) / PB11 (SDA) | Addr 0x76 |
| LCD1602 (via PCF8574) | I2C2 | PB10 (SCL) / PB11 (SDA) | Addr 0x27, shared bus |
| Switches 1–4 | GPIO | PB4–PB7 | PB4 remapped off NJTRST |
| Indicator LEDs 1–3 | GPIO | PA8–PA10 | |
| Heater relay | GPIO | PC9 | Low-side NPN drive (control side); AC contacts high-side on hot leg |
| Wi-Fi/BLE module | UART | TBD | Pending module selection — see ICD.md |

Full interface-level detail (register maps, protocols, framing) lives in
ICD.md, not here.

---

## 4. FreeRTOS Task Architecture

**Status: TBD — needs to be filled in from current firmware state.**

| Task | Priority | Responsibility | Status |
|---|---|---|---|
| (e.g., SensingTask) | TBD | Poll BME280 | TBD |
| (e.g., ControlTask) | TBD | Heater relay control loop | TBD |
| (e.g., DisplayTask) | TBD | LCD1602 update | TBD |
| (e.g., ConnectivityTask) | TBD | UART comms w/ Wi-Fi/BLE module (Models 3/4) | TBD |

Note: the prior 7-segment display architecture used a dedicated
osPriorityRealtime MuxTask driven by a TIM6 ISR (see ADR-0003). This was
eliminated with the LCD1602 migration — display updates are now expected
to run as a normal-priority periodic task rather than a real-time ISR-bound
one. Confirm actual task list/priorities against current firmware source
and fill in table above.

---

## 5. Data Flow (Control Loop, High-Level)

1. Sensing: BME280 read via I2C2 (temperature, humidity)
2. Control: compare sensed temperature against setpoint; drive heater
   relay (PC9) accordingly — on/off or duty-cycled
3. Display: current status/setpoint written to LCD1602 via PCF8574
4. Input: switch presses (PB4–PB7) update setpoint/mode
5. (Models 3/4) Connectivity: setpoint/status also exchanged with
   companion app via BLE, through the Wi-Fi/BLE module over UART

Exact task-to-task communication mechanism (queues, semaphores, shared
state + mutex) is TBD — fill in once implemented/confirmed in firmware.

---

## 6. Build Configuration Strategy

Single firmware project, per-model build configs (Models 1–4), enabled by
FreeRTOS extensibility.

| Model | Config differences vs. base |
|---|---|
| 1 | Temp control only — humidity/connectivity tasks excluded |
| 2 | + Humidity control |
| 3 | + Connectivity (Wi-Fi/BLE, Flutter app) |
| 4 | Same as 3, pro-consumer housing — UI TBD |

---

## 7. Revision Notes

This is a living document. Sections marked TBD should be filled in as the
firmware architecture solidifies — do not backfill speculatively.