# Changelog

All notable progress on this project, newest first.

## 2026-08-07
- Set up GitHub repo, README, ADR structure, folder layout.

## 2026-08-01
- FreeRTOS + CMSIS-RTOS v2 selected as RTOS approach (ADR 0001).
- Task architecture defined: TIM6 ISR → binary semaphore → MuxTask
  (osPriorityRealtime); Sensor Task (2s I2C poll) → queue; Display Manager Task
  (queue + button notifications).

## 2026-07-29 to 2026-07-30
- 7-segment lookup table finalized (byte-per-digit, a=MSB/dp=LSB, hex encoding).
- Display driver split into hardware-agnostic `seg_driver.c/.h` and board-specific
  `seg_board.c/.h`; BSRR-based atomic GPIO writes.
- API defined: `seg_display_set(unsigned int value, seg_display_type_t type, seg_temp_unit_t unit)`.

## 2026-07-28
- GPIO assignment settled: PC0-PC7 (segments), PB0-PB3 (digit select).
- NPN transistor per segment channel + P-MOSFET digit drive selected to stay
  under STM32C031 80 mA I/O current limit (ADR 0002).

## 2026-07 (earlier)
- MVP product spec locked: radiant/conductive plate heating, PETG enclosure,
  internal Flash NVM, PCB-mounted tactile switches, passive lift-off lid,
  mechanically-retained viewing window, PWM DC fan.
- Proofer_Spec_v2 generated with Locked/Open/Deferred status per section.
