# ADR-0004: ESP32-C6-MINI-1-N4 as Companion Wi-Fi/BLE Module

**Status:** Accepted
**Date:** 2026-08-21

## Context

Models 3 and 4 require Wi-Fi connectivity for the Flutter companion app,
with BLE used for Wi-Fi provisioning (see PRD.md, Connectivity section).
The architecture calls for a companion module that owns the radio stack
entirely, communicating to the STM32C031 over UART — the STM32 remains the
real-time control brain and never touches Wi-Fi/BLE directly (see
ARCHITECTURE.md Section 1).

Two constraints shaped module selection:

- **FCC:** the module must carry pre-existing FCC modular approval so the
  end product doesn't require its own intentional-radiator testing.
- **Bluetooth SIG (RISK-001):** using the module's BLE stack unmodified —
  no custom BLE application code — qualifies for a substantially cheaper
  SIG listing tier (~$2,500 one-time) versus full qualification
  (~$11,040/yr). This constrains not just module choice but how the
  BLE-based provisioning feature (including Wi-Fi reprovisioning via the
  Setup Menu, see ARCHITECTURE.md Section 4) is implemented.

## Decision

Use the **ESP32-C6-MINI-1-N4** (Wi-Fi 6 + BLE 5, 4MB flash, FCC/CE
pre-certified module) as the companion Wi-Fi/BLE module for Models 3/4.

Wi-Fi provisioning — including initial setup and reprovisioning — is
implemented using ESP-IDF's stock `wifi_provisioning` manager component
(`wifi_prov_scheme_ble`, built on `protocomm`), calling
`wifi_prov_mgr_start_provisioning()` and
`wifi_prov_mgr_reset_provisioning()` against the module's built-in
provisioning GATT service. No custom GATT service or custom BLE
application code is written. This keeps the design inside the "module
stack unmodified" mitigation path for RISK-001.

## Consequences

- ESP-IDF becomes the firmware framework for the companion module,
  locking in its toolchain and API surface (separate from the STM32
  side's CubeMX/CMSIS-RTOS v2 toolchain).
- Reprovisioning triggered from the Setup Menu (button-hold combo) maps
  directly to stock provisioning manager calls — no custom protocol
  design needed on the module side for this feature.
- STM32 ↔ module UART command set (ICD.md Section 3) now has a concrete
  target module to design pin assignment, baud rate, and framing against.
- RISK-001's mitigation path is now tied to a specific implementation
  choice (stock provisioning manager only) rather than a general
  intention — any future feature requiring custom BLE application code
  would need re-evaluation against the SIG fee-tier question before
  being added.
- Read/Update/Delete operations on stored Wi-Fi credentials route through
  stock provisioning manager mechanisms (re-provisioning overwrites
  stored config; `wifi_prov_mgr_reset_provisioning()` clears it) rather
  than custom protocomm endpoints — status/read functionality beyond what
  the stock framework provides remains an open item.

## Alternatives Considered

- **ESP32-C3-MINI-1:** Wi-Fi 4 (b/g/n) + BLE 5, RISC-V, lower cost, larger
  deployed base. Rejected in favor of the C6 for headroom, but remains a
  viable cost-down path if BOM pressure requires it later — no
  architectural rework needed since both run ESP-IDF's provisioning
  manager identically.
- **u-blox NORA-W1:** Same underlying ESP32-S3 silicon, u-blox's own
  module SKU with u-blox's certification/support/warranty chain. More
  expensive; considered as a second-source or long-term-availability
  option, not selected for initial design.
- **Custom BLE provisioning GATT service (hand-rolled):** Would allow
  bespoke Read/Update/Delete endpoints beyond what the stock provisioning
  manager offers. Rejected — this constitutes custom BLE application
  code, which moves the product out of the "module stack unmodified"
  mitigation path and into the full Bluetooth SIG qualification cost
  tier (RISK-001).