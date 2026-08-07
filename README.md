# Holding Cabinet — Consumer Bread Proofing Oven

Embedded firmware and electronics for a consumer bread-proofing oven to compete with the Brod and Taylor consumer product of the same type. 

## Project Goal

Design and build the electronics and firmware for a consumer proofing oven/box,
competitive with Brod & Taylor's proofer line, at a cost-competitive price point
for the US market. This repo tracks the alpha/pre-industrial-design stage through
firmware bring-up.

## Product Line

This isn't a single-SKU project. One PCBA and one firmware project (multiple
build configurations) are designed to serve four consumer models — enabled by
the FreeRTOS task architecture's ability to add capability without a hardware
or architecture redesign.

| Model | Features | Housing / UI |
|---|---|---|
| 1 | Temperature control | Shared |
| 2 | Temperature + humidity control | Shared |
| 3 | Temperature + humidity control, connectivity, companion app (iOS/Android, Flutter) | Shared |
| 4 (pro-consumer) | Same as Model 3 | Improved housing/insulation; UI TBD |

## Status

🚧 Active development — see [CHANGELOG.md](./CHANGELOG.md) for latest progress,
or [ROADMAP.md](./ROADMAP.md) for what's next, deferred, and out of scope.

## Hardware Overview

- **MCU (dev):** NUCLEO-L476RG (STM32L476)
- **MCU (target):** STM32C031C6
- **Display:** Kingbright CA56-12EWA common-anode 4-digit 7-segment LED, multiplexed
- **Heating:** Silicone rubber heater pad, bonded to anodized aluminum base plate
- **Sensing:** I2C temp/humidity sensor
- **Fan:** PWM-controlled DC muffin fan
- **Enclosure:** PETG, passive lift-off lid, mechanically-retained clear viewing window

See [`/hardware`](./hardware) for schematics, BOM, and datasheets.

## Firmware Architecture

- **RTOS:** FreeRTOS via CMSIS-RTOS v2 (CubeMX Generate Code, driven via the
  STM32Cube VS Code extension) — see [ADR 0001](./docs/adr/0001-freertos-over-bare-metal.md)
- **Display driver:** Hardware-agnostic `seg_driver.c/.h` + board-specific `seg_board.c/.h`,
  BSRR-based atomic GPIO writes for glitch-free multiplexing; segment lines driven
  through NPN transistors to stay under the MCU's I/O current limit — see
  [ADR 0002](./docs/adr/0002-npn-segment-drivers.md)
- **Tasks:** TIM6 ISR → binary semaphore → MuxTask (osPriorityRealtime); Sensor Task
  (2s I2C poll) → queue; Display Manager Task (queue + button notifications)

See [`/docs/adr`](./docs/adr) for the full set of design decisions and their trade-offs.

## Repo Structure

The STM32 project (`Core/`, `Drivers/`, `cmake/`, `CMakeLists.txt`, `.mxproject`,
`.vscode/`, `.settings/`) sits at the repo root — this is CubeMX's **Generate
Code** output, produced via the STM32Cube VS Code extension's CubeMX interface.
Docs live alongside it, not inside it:

```
Core/, Drivers/, cmake/,       CubeMX-generated project (STM32Cube VS Code
CMakeLists.txt, .mxproject,    extension front-end). Left exactly as generated.
.vscode/, .settings/
/docs/adr/                     Architecture Decision Records — why, not just what
/hardware/                     Schematics, BOM, datasheets
CHANGELOG.md                   Dated progress log
ROADMAP.md                     What's next, deferred, and out of scope
```

## Why This Project
TBD