# SEND Request Triage

**Sector:** Local Government  
**Category:** Casework  
**Status:** Experimental  
**Version:** 1.0.0  
**Licence:** MIT

---

## SPECIAL CATEGORY DATA

DO NOT use this flow in production without speaking to and seeking guidance from your Governance team or DPO.

## What this flow does

Triages incoming contacts to a local authority SEND (Special Educational Needs and Disabilities) team inbox. A coordinator pastes the text of an email or letter and the flow returns a structured triage note and a draft acknowledgement ready for review.

The flow runs four agents in sequence:

1. **Gate Check** — determines whether the contact is a valid SEND contact, detects safeguarding concerns, and classifies the contact type. Outputs a hard stop if a safeguarding concern is detected.
2. **Request Classifier** — extracts key identifiers (child name, DOB, school, EHC plan status, sender), assesses urgency, and calculates any applicable statutory deadline.
3. **Compliance Checker** — assesses statutory deadline risk, checks seven escalation triggers, identifies information gaps, and notes special category data handling requirements.
4. **Report Assembler** — synthesises all findings into a coordinator-ready SEND triage note and a tone-adapted draft acknowledgement.

---

## Contact types handled

| Type | Statutory clock |
|---|---|
| `ehc-needs-assessment-request` | 6 weeks — CFA 2014 s.36 |
| `annual-review-notification` | None on notification |
| `annual-review-outcome` | 4 weeks — SEND CoP 9.196 |
| `ehc-plan-amendment-request` | None |
| `transfer-of-plan` | 15 weeks — SEND CoP 9.157 |
| `ceasing-ehc-plan-request` | None |
| `personal-budget-request` | None |
| `mediation-referral` | Escalate immediately |
| `general-enquiry` | None |
| `complaint` | None (route to complaints process) |
| `professional-advice-submission` | None |
| `out-of-area-placement-query` | None |

---

## Outputs

- **SEND Triage Note** — structured internal note covering contact type, child and sender identifiers, plain-English summary, statutory deadline, deadline risk, escalation flags, routing, information gaps, and data handling note
- **Draft Acknowledgement** — tone-adapted for the sender type (warm for families, formal for professionals), ready for coordinator review before dispatch
- **OUT_OF_SCOPE** — structured routing note if the contact does not belong to the SEND service
- **SAFEGUARDING_REFERRAL** — immediate referral notice if a safeguarding concern is detected; all processing stops and the coordinator is instructed to contact the MASH

---

## Human review requirements

**All outputs must be reviewed by a trained SEND coordinator before any action is taken.** Key checks:

1. Verify the contact type classification — misclassifying a statutory request may cause the authority to miss a statutory deadline with legal consequences
2. Confirm the statutory clock start date and calculate the exact deadline against the case management system — the flow's deadline calculations are indicative only and do not account for bank holidays or school holidays
3. For transfer-of-plan contacts, confirm the actual date the child moved into the area before calculating the 15-week deadline — see Limitations below
4. Check child records in the case management system before acting — the flow has no access to live case data
5. Review the draft acknowledgement before sending — do not dispatch automatically
6. Any escalation flag must be reviewed with the SEND Manager before the contact is assigned to a coordinator's caseload
7. If a `SAFEGUARDING_REFERRAL` is returned, refer to the MASH immediately and do not continue with SEND triage

---

## Data sensitivity and governance

This flow processes **special category personal data** under UK GDPR Article 9, including health diagnoses, educational psychology assessments, and social care information relating to children.

- A **Data Protection Impact Assessment (DPIA)** is likely required before deploying in a live environment
- **Amazon Bedrock** is the recommended LLM provider for production — it provides data residency controls and enterprise DPAs suitable for UK public sector
- **OpenAI** can be used for development and testing only — do not use with real case data without DPO sign-off and a reviewed data processing agreement covering special category data
- Ephemeral memory is enabled — no data persists beyond the active session

See [docs/governance.md](../../docs/governance.md) for the minimum governance steps required before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- [Children and Families Act 2014](https://www.legislation.gov.uk/ukpga/2014/6/contents)
- [SEND Code of Practice 0–25 (2015)](https://www.gov.uk/government/publications/send-code-of-practice-0-to-25)
- [Education Act 1996 — s.19 (education out of school)](https://www.legislation.gov.uk/ukpga/1996/56/section/19)
- [UK GDPR — Article 9 (special category data)](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/lawful-basis/a-guide-to-lawful-basis/lawful-basis-for-processing/special-category-data/)

---

## Platform implementations

| Platform | Status |
|---|---|
| Flowise | ✅ Tested (v3.0.5) |

---

## Limitations

- The flow has no access to your case management system — it cannot check whether a child is known to the service, whether an EHC plan exists, or the current status of any case
- Deadline calculations are indicative only and do not account for bank holidays, school holidays, or other calendar exceptions — all deadlines must be verified by the coordinator
- **Transfer-of-plan clock start date:** the 15-week clock under SEND CoP 9.157 runs from the date the child moves into the authority's area, not the date the contact is received. Where the contact does not state the exact move date, the flow uses the date of the contact as the clock start — this will underestimate how much time has already elapsed. Coordinators must confirm the actual move date and recalculate the deadline accordingly
- **Transfer-of-plan deadline arithmetic:** the flow's 15-week deadline calculation is particularly prone to error and must always be verified independently
- The flow cannot make decisions about a child's entitlement to SEND support — it is a triage tool only
- The draft acknowledgement is a starting point — the coordinator must review and adjust before sending
