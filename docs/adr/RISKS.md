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

**Mitigation path:**
Use module's BLE stack unmodified (no custom BLE application code) to
enable "qualification without testing" via the module vendor's existing
QDID. Reported cost: ~$2,500 one-time (Product Listing Fee, <$1M revenue
tier, module stack unmodified) vs. ~$11,040/yr (full qualification).

**Action needed:** Confirm actual obligation and cost with regulatory/IP
counsel before production commitment. Do not finalize BLE module firmware
approach (stock stack vs. custom) until resolved, since that choice
determines which fee tier applies.

---

<!-- Next entry: RISK-002 -->
