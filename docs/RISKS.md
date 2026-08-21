# Risk Log — Holding Cabinet (Proofer)

Living doc. Covers technical, regulatory, and sourcing risk in one place.
Add entries as identified; close out or downgrade as resolved. Not a
formal FMEA — lightweight tracking for a solo/small-team Agile project.

Status legend: 🔴 Open | 🟡 In Progress | 🟢 Resolved/Closed

---

## RISK-001: Bluetooth SIG Qualification/Licensing

**Category:** Regulatory / Compliance
**Affects:** Models 3, 4
**Status:** 🟡 In Progress

Product uses BLE for Wi-Fi provisioning. Bluetooth SIG trademark license
terms may require SIG membership and product qualification/listing,
independent of whether the Bluetooth name/logo appears on the product.

**Module:** ESP32-C6-MINI-1-N4 (see ADR-0004). Provisioning implementation
uses only stock ESP-IDF `wifi_provisioning` manager calls — no custom BLE
application code — keeping this within the cheaper qualification tier.

**Mitigation path:** Module stack unmodified → qualification via module
vendor's existing QDID. Reported cost: ~$2,500 one-time (<$1M revenue
tier) vs. ~$11,040/yr (full qualification).

**Action needed:** Confirm actual obligation and cost with regulatory/IP
counsel before production commitment.

---

## RISK-002: UL Recognized Component Sourcing for AC Heater Circuit

**Category:** Regulatory / Compliance, Sourcing
**Affects:** All models
**Status:** 🔴 Open — deferred

AC heater pad, switching relay, and power supply sit in a UL-critical
circuit area. Non-UL DC heater pads are cheap and plentiful; genuine
UL File Number parts historically require more vendor digging and hit
cost/MOQ walls.

**Mitigation path:** Defer UL sourcing decision until heater pad
wattage/voltage and relay selection are more mature. Track vendor UL
File Number availability during standard sourcing.

**Action needed:** None yet — revisit once AC heater pad vendor is
otherwise selected on cost/spec fit.

---

## RISK-003: Base Plate Attachment Method Undefined

**Category:** Mechanical
**Affects:** All models
**Status:** 🔴 Open — deferred

Housing surrounds the base plate but attachment method is TBD — affects
assembly, serviceability, and thermal isolation between the heated plate
and PETG housing.

**Mitigation path:** Defer to hardware-in-hand; evaluate fasteners,
adhesive, snap-fit once prototype base plate and housing are both available.

---

## RISK-004: Setup Menu False-Trigger

**Category:** Firmware / UX
**Affects:** All models (button combo); reprovisioning specifically Models 3, 4
**Status:** 🔴 Open

Setup Menu entered by holding SW1+SW2 for 3–5s at power-on. A false
trigger could put the unit into setup mode unexpectedly, or for
reprovisioning specifically, disconnect a working unit from its network
without user intent.

**Mitigation path:** 3–5s hold threshold, sampled early in boot. Consider
an LCD confirmation step before executing any destructive menu action
rather than acting on entry alone.

**Action needed:** Verify hold-threshold behavior against real hardware;
add to TEST_PLAN.md once test cases are written.

---

## RISK-005: FCC Modular Approval Integration

**Category:** Regulatory / Compliance
**Affects:** Models 3, 4
**Status:** 🟡 In Progress

Wi-Fi/BLE module (ESP32-C6-MINI-1-N4, see ADR-0004) carries FCC modular
approval (Part 15C), avoiding full end-product radiator testing — but
only if integration follows the module vendor's grant conditions.

**Action needed:** Confirm the module's FCC ID and modular grant cover
the intended antenna/enclosure configuration; confirm end-product
labeling requirements (FCC ID pass-through vs. separate labeling).

---

## Not Yet Tracked

Placeholder for risk areas not yet assessed: state-level appliance
regulations, California Prop 65, packaging/labeling, international
certification if a non-US market is pursued.