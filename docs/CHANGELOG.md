# Changelog

All notable changes to the Holding Cabinet firmware will be documented in this file.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## Versioning

This project uses [Semantic Versioning](https://semver.org/) (MAJOR.MINOR.PATCH).

- **MAJOR** — incompatible/breaking changes (e.g., non-backward-compatible
  hardware revision, breaking protocol change with the companion app)
- **MINOR** — new functionality, backward compatible
- **PATCH** — backward-compatible bug fixes only

The firmware version is shared across all four product models (single
PCBA/firmware codebase, per-model build configuration). Model identity is
carried as build metadata, not a separate version number:


All four models built from the same tagged commit share the same
MAJOR.MINOR.PATCH; only the build metadata suffix differs.

1.2.0+model1
1.2.0+model2
1.2.0+model3
1.2.0+model4

---

## [Unreleased]

### Added
- Companion Wi-Fi/BLE module selected: ESP32-C6-MINI-1-N4 (see ADR-0004)
- Setup Menu spec: SW1+SW2 (Up+Down) held 3–5s at power-on enters menu;
  first defined action is Wi-Fi reprovisioning (see ARCHITECTURE.md
  Section 4, PRD.md Setup Menu section)
- Reprovision Wi-Fi UART command + response stubbed in ICD.md Section 3
  (opcode/frame format still TBD)
- RISK-004: Setup Menu false-trigger risk

### Changed
- RISK-001 mitigation path confirmed against a specific implementation:
  Wi-Fi reprovisioning uses only stock ESP-IDF `wifi_provisioning`
  manager calls, no custom BLE application code

### Fixed
- 

### Removed
- 

---

<!--
Entry template for future releases:

## [x.y.z] - YYYY-MM-DD

### Added
- 

### Changed
- 

### Fixed
- 

### Removed
- 
-->