# ADR 0001: FreeRTOS over bare-metal for MCU firmware

**Status:** Accepted
**Date:** 2026-08-01

## Context

The controller needs to handle several concurrent, timing-sensitive activities:
multiplexed 7-segment display refresh (ISR-driven, must not visibly flicker or
glitch), periodic I2C sensor reads, button input, and PWM fan control. 

The product line is architected as a single PCBA and firmware project (multiple
build configurations) spanning four consumer models — Model 3 and Model 4 add
connectivity (companion iOS/Android app), which will introduce additional
concurrent activity beyond the base task set.

## Decision

Use FreeRTOS via CMSIS-RTOS v2, generated through STM32CubeMX into STM32CubeIDE,
rather than bare-metal superloop + ISR design.

## Consequences

- Clean task separation (MuxTask, Sensor Task, Display Manager Task) instead of
  a single superloop with growing complexity — avoids the "blob code in one task"
  failure mode seen on a prior commercial product.
- Requires care with ISR-to-task signaling (binary semaphores, FromISR APIs) and
  correct NVIC preemption priority configuration (must be ≥5, within
  configMAX_SYSCALL_INTERRUPT_PRIORITY) — a non-obvious constraint that will trip
  up anyone unfamiliar with FreeRTOS interrupt priority rules.
- Adds RTOS overhead (RAM/flash, context switching) that isn't needed on a truly
  simple product, but is justified here given planned networking growth.

## Alternatives Considered

- **Bare-metal superloop + ISR:** simpler, less overhead, but concurrency logic
  tends to sprawl into an unmaintainable single task as features are added.
- **Bare-metal with cooperative scheduler:** middle ground, but reinvents a worse
  version of what FreeRTOS already provides for free via CubeMX generation.
