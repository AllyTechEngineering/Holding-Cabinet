# Product Requirements Document — Holding Cabinet (Proofer)

**Status:** Living document — iterated per Agile approach, not fully locked up front
**Last updated:** 2026-08-15

## 1. Purpose

Consumer bread proofing oven/holding cabinet. Primary goal: commercialize as a
viable product with solid ROI. Secondary/parallel benefit: building embedded
skills that keep employment options open — not a portfolio-first project.

Target market: US, cost-sensitive, competing with Brod & Taylor's proofer line.

## 2. Product Line Architecture

Single PCBA + single firmware project (multiple build configs), shared across
4 consumer models, enabled by FreeRTOS build-config extensibility.

| Model | Temp Control | Humidity Control | Connectivity (Wi-Fi/BLE + App) | Housing/Insulation |
|-------|:---:|:---:|:---:|---|
| 1 | ✅ | — | — | Standard |
| 2 | ✅ | ✅ | — | Standard |
| 3 | ✅ | ✅ | ✅ | Standard |
| 4 (pro-consumer) | ✅ | ✅ | ✅ | **TBD** |

Models 1–3 share housing and UI. Model 4 is tabled — TBD pending decisions
on improved housing/insulation and UI.

## 3. Locked Decisions (MVP-level)

- Radiant/conductive plate heating: AC silicone heater pad on anodized
  aluminum base plate — single pad, 200W @ 120VAC (300W TBD alternate,
  pending thermal testing). Sourced from high-volume consumer AC silicone
  heater market (oil pan pre-heaters, 3D printer bed heaters). Pad does
  not need to reach base plate edges — margin acceptable, uneven heat
  distribution across plate is a don't-care. AC switching: relay/contactor
  on hot (black) leg only; neutral/ground unswitched. UL certification
  deferred — see COMPLIANCE.md.
- Interior cavity dimensions: 15" x 12" x 8"
- Base plate mounting (prototype): rubber feet, ~0.75" standoff from
  countertop to underside of plate. Housing surrounds base plate, attaches
  via TBD method, extends near-flush to countertop.
- PETG enclosure
- STM32 internal Flash for NVM
- PCB-mounted tactile switches, dome-actuated through membrane
- Fixed front wall for UI panel
- Passive lift-off lid
- Mechanically-retained clear insert viewing window in lid
- Humidity port provision (capped in MVP)
- PWM-controlled DC muffin fan
- MCU: STM32C031C6 (production), STM32L476/NUCLEO-L476RG (dev)
- Display: SunFounder LCD1602 (HD44780U) over I2C via PCF8574 backpack — see ADR-0003

## 4. Open / Deferred (pending physical testing)

- Outer dimensions (interior cavity locked at 15" x 12" x 8"; outer TBD)
- Power supply wattage confirmation (200W @ 120VAC locked for heater; 300W
  alternate TBD pending testing); base plate attachment method TBD
- AC heater pad vendor voltage confirmation (initial listings reviewed
  specify 12V/24V/220V; 120VAC confirmation pending vendor communication)
- Fan placement and control strategy
- BME280 sensor placement
- Mode Select button function
- Error codes
- Lid window material
- Feet design
- Graphic design
- Model 4 housing/insulation and UI

## 5. Connectivity (Models 3–4)

- Provisioning flow: BLE advertising → mobile app pairing → app supplies
  Wi-Fi SSID/password to embedded system → device connects to home LAN
- Architecture: companion Wi-Fi/BLE module owns the radio stack; STM32C031
  stays the real-time control brain and talks to the module over UART
- Module: ESP32-C6-MINI-1-N4 (Wi-Fi 6 + BLE 5, FCC/CE pre-certified)
- Module stack to be used unmodified (no custom BLE application code) —
  see RISK-001 for why this matters
- App: Flutter (cross-platform iOS/Android)
- CRUD support required for Wi-Fi provisioning: initial setup (Create),
  connection status query (Read), change network (Update), forget
  network / re-provision (Delete) — see ICD.md Section 3–4 for interface
  detail

## 6. Setup Menu

Physical setup menu accessible without the companion app, entered by
holding SW1 (Up) + SW2 (Down) for 3–5 seconds at power-on. Provides local
recovery path for scenarios where BLE/app access isn't available or
practical (e.g. router replaced, no phone on hand).

First defined menu action: **Reprovision Wi-Fi** — clears stored network
credentials and returns the device to BLE advertising/provisioning mode.

Remaining menu structure and additional menu items: TBD.

## 7. Explicitly Out of Scope (future iterations)

- Ultrasonic humidifier
- Dough rise indicator (potential IP considerations — flag before pursuing)

## 8. Open Questions

- Model 4 full spec