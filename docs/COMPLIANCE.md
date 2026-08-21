# Compliance

Tracks regulatory certification and licensing requirements for the Holding
Cabinet product line. Distinct from `RISKS.md`, which covers general
technical/project risk — this document is scoped to legal/regulatory
compliance obligations specifically.

Status legend: 🔴 Not Started | 🟡 In Progress | 🟢 Resolved/Closed

---

## Bluetooth SIG Qualification

**Status:** 🟡 In Progress (see RISK-001 in RISKS.md for full detail)
**Affects:** Models 3, 4 (connectivity variants)

Product uses BLE for Wi-Fi provisioning. Bluetooth SIG trademark license
terms may require SIG membership and product qualification/listing,
independent of whether the Bluetooth name/logo appears on the product.

**Module:** ESP32-C6-MINI-1-N4 (selected, see ADR-0004). Wi-Fi
provisioning — including reprovisioning via the Setup Menu — uses only
stock ESP-IDF `wifi_provisioning` manager calls against the module's
built-in provisioning GATT service. No custom BLE application code.

**Mitigation path:** Use the Wi-Fi/BLE module's BLE stack unmodified (no
custom BLE application code) to qualify via the module vendor's existing
QDID rather than pursuing independent qualification.

**Open items:**
- Confirm ESP32-C6-MINI-1-N4's QDID and qualification listing
- Confirm actual obligation and cost with regulatory/IP counsel before
  production commitment (reported: ~$2,500 one-time Product Listing Fee,
  <$1M revenue tier, module stack unmodified, vs. ~$11,040/yr full
  qualification)

---

## FCC (Radio/Connectivity)

**Status:** 🟡 In Progress
**Affects:** Models 3, 4 (connectivity variants)

Wi-Fi/BLE module selection requires FCC modular approval (pre-certified
module) to avoid full FCC certification testing at the end-product level.

**Module:** ESP32-C6-MINI-1-N4 (selected, see ADR-0004) — carries FCC
modular approval (Part 15C).

**Open items:**
- Confirm module's FCC ID and modular approval grant covers intended
  antenna/enclosure configuration on the Holding Cabinet PCBA/housing
- Confirm end-product labeling requirements (FCC ID pass-through vs.
  separate device labeling)

---

## UL (Electrical Safety)

**Status:** 🔴 Not Started — deferred
**Affects:** All models (heater, relay, AC mains wiring, power supply)

Critical-circuit components (AC silicone heater pad, switching relay,
power supply) should be sourced with traceable UL Recognized Component
status (UL File Number) to avoid a certification gap at the end-product
level.

**Open items:**
- Confirm UL File Number availability for selected AC heater pad vendor
- Confirm UL File Number for relay/switching component
- Confirm UL File Number or listing for power supply
- Determine target end-product UL certification path (e.g., UL 1026,
  UL 60335, or applicable standard) — deferred pending further design
  maturity

---

## Other

**Status:** 🔴 Not tracked yet

Placeholder for additional compliance areas as they arise (e.g., state-level
appliance regulations, California Prop 65, packaging/labeling requirements,
international certification if Canada market is pursued per PRD).