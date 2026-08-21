# Risk Log — Holding Cabinet (Proofer)

Living doc. Add entries as they're identified; close out or downgrade as
resolved. Not a formal FMEA — lightweight tracking for a solo/small-team
Agile project.

---

## RISK-001: Bluetooth SIG Qualification/Licensing

**Category:** Regulatory / Compliance
**Affects:** Models 3, 4 (connectivity variants)
**Status:** Open

**Description:**
Product uses BLE for Wi-Fi provisioning. Per Bluetooth SIG trademark license
terms, use of Bluetooth wireless technology may require SIG membership
(free) and product qualification/listing, independent of whether the
Bluetooth name/logo appears on the product. Community reports (unverified
against SIG directly) suggest declining to reference "Bluetooth" anywhere
reduces enforcement exposure but is not a guaranteed exemption.

**Module:** ESP32-C6-MINI-1-N4 (selected). Confirmed the Wi-Fi
provisioning design (including reprovisioning via the setup menu) uses
only stock ESP-IDF `wifi_provisioning` manager calls
(`wifi_prov_mgr_reset_provisioning()` / `wifi_prov_mgr_start_provisioning()`)
against the module's built-in provisioning GATT service — no custom BLE
application code. This keeps the design within the "module stack
unmodified" mitigation path below.

**Mitigation path:**
Use module's BLE stack unmodified (no custom BLE application code) to
enable "qualification without testing" via the module vendor's existing
QDID. Reported cost: ~$2,500 one-time (Product Listing Fee, <$1M revenue
tier, module stack unmodified) vs. ~$11,040/yr (full qualification).

**Action needed:** Confirm actual obligation and cost with regulatory/IP
counsel before production commitment. Module and provisioning approach are
now locked; remaining open item is legal confirmation of fee tier, not a
design decision.
---
## RISK-002: UL Recognized Component Sourcing for AC Heater Circuit

**Category:** Regulatory / Compliance, Sourcing
**Affects:** All models
**Status:** Open

**Description:**
AC silicone heater pad, switching relay, and power supply sit in a
UL-critical circuit area (mains-adjacent AC heating). Based on prior
sourcing experience, non-UL-recognized DC heater pads are widely available
at low cost, but parts carrying a genuine UL File Number typically require
more vendor digging and direct communication, and historically come down
to a cost or MOQ constraint.

**Mitigation path:**
Defer UL sourcing decision until heater pad wattage/voltage and relay
selection are more mature. Track vendor UL File Number availability as
part of standard component sourcing for this circuit going forward.

**Action needed:** None yet — explicitly deferred per Agile approach.
Revisit once AC heater pad vendor is otherwise selected on cost/spec fit.

**See also:** COMPLIANCE.md, UL (Electrical Safety) section.
---
## RISK-003: Base Plate Attachment Method Undefined

**Category:** Mechanical
**Affects:** All models
**Status:** Open

**Description:**
Housing surrounds the base plate and must attach to it, but the attachment
method is TBD. This affects assembly process, serviceability, and possibly
thermal isolation between the heated base plate and the PETG housing.

**Mitigation path:**
Defer to hardware-in-hand per Agile approach; evaluate options (mechanical
fasteners, adhesive, snap-fit, etc.) once prototype base plate and housing
are both in hand.

**Action needed:** None yet — explicitly deferred.
---
## RISK-004: Setup Menu False-Trigger

**Category:** Firmware / UX
**Affects:** All models (button combo), reprovisioning specifically Models 3, 4
**Status:** Open

**Description:**
Setup menu is entered by holding SW1 (Up) + SW2 (Down) for 3–5 seconds at
power-on. A false trigger — accidental combo detection during normal
power-on, or an unintended short-hold registering as a valid combo — could
put the unit into setup mode unexpectedly, and for the Wi-Fi reprovision
action specifically, could disconnect a working unit from its network
without user intent.

**Mitigation path:**
Hold-duration threshold (3–5s) sized to avoid accidental trigger from a
brief press during normal power-on. Button state must be sampled early in
boot before the rest of peripheral init runs, so this needs to be verified
against actual boot-time GPIO read timing once implemented. Consider
requiring a confirmation step on the LCD before executing any destructive
menu action (e.g., reprovision) rather than acting on entry alone.

**Action needed:** Verify hold-threshold behavior against real hardware
once implemented; add to TEST_PLAN.md as a dedicated test case (positive:
combo triggers menu; negative: brief/incidental press does not).
