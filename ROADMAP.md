# Roadmap

## About this project

Embedded firmware and electronics for a consumer bread-proofing oven (holding
cabinet), being developed for commercial release. Built using current embedded
systems practices (STM32, FreeRTOS, hardware bring-up, driver design).

## Status

Firmware bring-up phase. Product spec (MVP) is drafted with locked/open/deferred sections. Display driver architecture is designed; FreeRTOS project setup and sensor/display task implementation are next.

For a dated, granular log of how this was reached, see [CHANGELOG.md](./CHANGELOG.md).

## Done

**Product line architecture (locked):**
- Single PCBA + single firmware project (multiple build configurations) shared across all 4 consumer models
- Model 1: temperature control only
- Model 2: temperature + humidity control
- Model 3: temperature + humidity control, connectivity, companion iOS/Android app (Flutter)
- Model 4 (pro-consumer): same feature set as Model 3, improved housing/insulation
- Models 1–3 share housing and UI

**Product spec (MVP-locked):**
- Radiant/conductive heating: silicone rubber heater pad on anodized aluminum base plate
- PETG enclosure
- STM32 internal Flash for non-volatile storage
- PCB-mounted tactile switches, dome-actuated through membrane
- Fixed front wall for UI panel
- Passive lift-off lid with mechanically-retained clear viewing window
- Humidity port provision (capped in MVP)
- PWM-controlled DC fan

**Firmware / hardware architecture:**
- MCU: STM32C031C6 (target), NUCLEO-L476RG (dev board)
- Display: Kingbright CA56-12EWA common-anode 4-digit 7-segment LED
- Display driver circuit designed — P-channel MOSFETs (AO3401A) for digit-select high-side switching, NPN transistors (MMBT3904) per segment to stay within GPIO sink current limits — see [ADR 0002](./docs/adr/0002-npn-segment-drivers.md)
- GPIO assignment finalized: PC0–PC7 (segments), PB0–PB3 (digit select)
- 7-segment lookup table finalized (byte-per-digit encoding, 2 numeric digits + unit letter)
- Display driver split into hardware-agnostic (`seg_driver.c/.h`) and board-specific (`seg_board.c/.h`) layers
- RTOS selected: FreeRTOS via STM32CubeMX + CMSIS-RTOS v2 — see [ADR 0001](./docs/adr/0001-freertos-over-bare-metal.md)
- Task architecture defined: TIM6 ISR → mux task (real-time priority), sensor task (I2C poll every 2s), display manager task (queue + button notifications)

Full schematics, BOM, and datasheets: [`/hardware`](./hardware). Full decision log: [`/docs/adr`](./docs/adr).

## In progress

- STM32CubeMX project configuration (TIM6, FreeRTOS, GPIO)
- Display driver implementation and bring-up on NUCLEO-L476RG
- Transitioning active firmware development into the generated STM32CubeIDE project

## Next up

- Complete CubeMX-generated project scaffold
- Bring up mux task + display driver end-to-end on hardware
- Implement sensor task (I2C reads) and wire into display manager task
- Button/mode-select handling (kept outside the display driver layer by design)

## Deferred (pending physical testing)

These are intentionally unresolved until real enclosures/hardware can be tested:

- Outer dimensions
- Power supply voltage/wattage
- Fan placement and control strategy
- BME280 sensor placement
- Mode Select button function
- Error codes
- Lid window material
- Feet design
- Graphic design / industrial design pass
- Model 4 UI (pro-consumer variant)

## Explicitly out of scope (for now)

Not being designed for in this revision — noted here so scope doesn't creep:

- Humidity control / ultrasonic humidifier
- Wi-Fi / companion app
- Dough rise indicator (potential IP considerations)

## Why This Project
Primary goal: bring a cost-competitive consumer proofing oven to market with a
solid ROI. Applying disciplined engineering and project-management practices
throughout — schematic capture, driver design under real hardware constraints
(GPIO current limits, display multiplexing, RTOS task/ISR interaction), and
rigorous spec management.
