# Product Requirements Document — Holding Cabinet (Proofer)

**Status:** Living document — iterated per Agile approach, not fully locked up front
**Last updated:** 2026-08-07

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

- Radiant/conductive plate heating: silicone rubber heater pad on anodized
  aluminum base plate
- PETG enclosure
- STM32 internal Flash for NVM
- PCB-mounted tactile switches, dome-actuated through membrane
- Fixed front wall for UI panel
- Passive lift-off lid
- Mechanically-retained clear insert viewing window in lid
- Humidity port provision (capped in MVP)
- PWM-controlled DC muffin fan
- MCU: STM32C031C6 (production), STM32L476/NUCLEO-L476RG (dev)
- Display: Kingbright CA56-12EWA common-anode 4-digit 7-segment

## 4. Open / Deferred (pending physical testing)

- Outer dimensions
- Power supply voltage/wattage
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
- Module stack to be used unmodified (no custom BLE application code) —
  see RISK-001 for why this matters
- App: Flutter (cross-platform iOS/Android)

## 6. Explicitly Out of Scope (future iterations)

- Ultrasonic humidifier
- Dough rise indicator (potential IP considerations — flag before pursuing)

## 7. Open Questions

- Wi-Fi/BLE module selection — narrowing in progress (see ADR once decided)
- Model 4 full spec
