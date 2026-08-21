# Holding Cabinet — Bread Proofer/Holding Cabinet

Consumer bread proofing and holding cabinet. Single PCBA and firmware
codebase, four build-configured product models, targeting the US
consumer market (competitive reference: Brod & Taylor proofer line).

## Product Line

| Model | Temp Control | Humidity Control | Connectivity + App |
|-------|:---:|:---:|:---:|
| 1 | ✅ | — | — |
| 2 | ✅ | ✅ | — |
| 3 | ✅ | ✅ | ✅ |
| 4 (pro-consumer) | ✅ | ✅ | ✅ |

## Architecture

Radiant/conductive plate heating (AC silicone heater pad on anodized
aluminum base plate), controlled by an STM32C031C6 running FreeRTOS.
Models 3/4 add an ESP32-C6-MINI-1-N4 companion module for Wi-Fi/BLE,
communicating to the STM32 over UART. Full system detail in
[ARCHITECTURE.md](docs/ARCHITECTURE.md).

## Repo Structure
docs/
├── adr/ — architecture decision records
├── reference/ — datasheet/IC reference notes
├── PRD.md — what the product is and does
├── ARCHITECTURE.md — how the system is built (includes interfaces)
├── RISKS.md — open technical, regulatory, and sourcing risks
└── CHANGELOG.md — version history
## Status

Active development, pre-production. See [PRD.md](docs/PRD.md) for current
scope and open items, [RISKS.md](docs/RISKS.md) for what's unresolved.