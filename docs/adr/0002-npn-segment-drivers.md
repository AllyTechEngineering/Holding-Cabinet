# ADR 0002: NPN transistor per segment channel instead of direct GPIO drive

**Status:** Accepted
**Date:** 2026-07-28

## Context

The Kingbright CA56-12EWA is a common-anode 4-digit display, multiplexed across
GPIOC (segments, PC0-PC7) and GPIOB (digit select, PB0-PB3). The target MCU
(STM32C031) has an absolute maximum total I/O sink current of 80 mA. Direct GPIO
segment drive risks violating this under worst-case load (multiple digits/segments
active near-simultaneously during multiplex refresh).

## Decision

Drive each segment channel through an NPN transistor (MMBT3904, SOT-23, 2.2 kΩ
base resistor) rather than sinking LED current directly through GPIO pins.
Digit-common high-side lines are switched via P-channel MOSFETs (AO3401A, 220 Ω
gate series resistor, 10 kΩ gate pull-up to VCC).

## Consequences

- GPIO pins only source/sink small transistor base/gate currents — total I/O
  current stays safely under the 80 mA absolute max regardless of display pattern.
- Adds 8 NPN transistors + 4 P-MOSFETs and associated passives to the BOM and
  board area, vs. a simpler (but current-limit-violating) direct-drive design.
- Common-anode topology must be respected throughout — a labeling error here
  (CA vs. CC) previously caused significant rework and is now caught at the
  part-number level as a standing check.

## Alternatives Considered

- **Direct GPIO drive:** simplest, cheapest, but violates absolute max I/O current
  under worst-case multiplex load — rejected.
- **Dedicated LED driver IC (e.g., constant-current segment driver):** more
  headroom and cleaner brightness control, but adds cost and complexity not
  justified at this display size/segment count for MVP.
