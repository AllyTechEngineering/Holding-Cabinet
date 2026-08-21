# Test Plan — Holding Cabinet (Proofer)

Living doc. Verification plan for firmware, hardware, and system-level
requirements. Not formal DVP&R — lightweight tracking suited to a
solo/small-team Agile project, expanded toward formality as the product
approaches production.

Status legend: 🔴 Not Started | 🟡 In Progress | 🟢 Complete

---

## 1. Thermal Performance

**Status:** 🔴 Not Started

Verification that the base plate/heater assembly meets proofing/holding
temperature requirements within the 15" x 12" x 8" interior cavity.

**Open items:**
- Heat-up time to target setpoint
- Steady-state temperature stability / duty cycle behavior
- Air temperature vs. base plate setpoint discrepancy characterization
  (expected per Brod & Taylor reference behavior — see PRD Section 2)
- Confirm 200W @ 120VAC is sufficient; evaluate 300W alternate if not
- Hot/cold spot mapping across base plate surface

---

## 2. Electrical Safety

**Status:** 🔴 Not Started

**Open items:**
- AC relay/contactor switching verification (hot leg only, neutral/ground
  unswitched)
- Fail-safe default-off behavior under fault conditions
- Thermal cutoff/fuse verification (heater pad built-in protection, if
  present)
- Isolation/clearance verification between AC mains wiring and
  low-voltage control electronics

---

## 3. Sensing Accuracy

**Status:** 🔴 Not Started

**Open items:**
- BME280 temperature/humidity reading accuracy and placement verification
- Correlation between BME280 ambient reading and actual dough/container
  temperature (informs user-facing documentation per PRD Section 2)

---

## 4. Display & UI

**Status:** 🔴 Not Started

**Open items:**
- LCD1602 I2C init sequence verification across power cycles
- Switch debounce and response verification (PB4–PB7)
- Indicator LED behavior verification (PA8–PA10)
- Mode Select button behavior (pending function definition — see PRD
  Section 4)
- Setup Menu button-combo threshold (RISK-004): positive test — SW1+SW2
  held 3–5s at power-on triggers Setup Menu; negative test — brief or
  incidental press during normal power-on does not trigger it. Verify
  against actual boot-time GPIO sampling timing once implemented.

---

## 5. Fan / Air Circulation

**Status:** 🔴 Not Started — deferred pending fan placement/control
strategy decision (see PRD Section 4)

---

## 6. Connectivity (Models 3/4)

**Status:** 🔴 Not Started

**Open items:**
- BLE advertising and pairing verification (ESP32-C6-MINI-1-N4)
- Wi-Fi provisioning flow (SSID/password handoff, LAN connection)
- Wi-Fi reprovisioning flow via Setup Menu (reset + re-advertise, see
  ARCHITECTURE.md Section 4 and ICD.md Section 3)
- STM32 ↔ module UART link reliability (see ICD.md)
- App ↔ device data exchange verification

---

## 7. Firmware / RTOS

**Status:** 🔴 Not Started

**Open items:**
- Task priority/timing verification under FreeRTOS
- Build-config verification across all four model targets
- NVM (internal Flash) read/write reliability

---

## 8. Regulatory Pre-Compliance

**Status:** 🔴 Not Started

**Open items:**
- FCC pre-scan (ESP32-C6-MINI-1-N4 is pre-certified — verify end-product
  integration follows module vendor's grant conditions, see RISK-001)
- Bluetooth SIG qualification tier confirmation (see RISK-001)
- UL Recognized Component documentation check for heater/relay/power
  supply (see COMPLIANCE.md)

---

<!-- Add new sections/entries as test scope is defined -->