# FOI Request Triage

Triages incoming Freedom of Information (FOIA) and Environmental Information Regulations (EIR) requests for UK public sector organisations. Validates, classifies, routes, and flags potential exemptions — and produces a draft acknowledgement email — giving FOI coordinators a structured starting point for every request.

---

## What it does

1. **Validates** whether the input is a genuine FOI or EIR request — rejects out-of-scope inputs (complaints, subject access requests, general enquiries, unrelated text) with a clear explanation and suggested handling
2. **Classifies the regulatory regime** — determines whether the request falls under the Freedom of Information Act 2000 or the Environmental Information Regulations 2004, overriding incorrect citations by the requester where necessary
3. **Extracts and structures** what is being asked — produces a plain-English summary and a numbered list of distinct information requests
4. **Routes to the responsible service area(s)** — maps the request to the relevant council department(s) and flags multi-department requests
5. **Assesses complexity and risk** — rates overall complexity (Simple / Moderate / Complex), flags political sensitivity, calculates the 20 working day response deadline, and flags vexatious (S.14), cost limit (S.12), repeated, and round-robin requests
6. **Flags potential exemptions** — identifies FOIA exemptions (S.21, S.31, S.36, S.40, S.41, S.42, S.43 and others) or EIR exceptions (Reg. 12/13) that the coordinator should consider, with reasons specific to the request
7. **Produces a triage report** — a structured internal document covering all of the above, ready to act on
8. **Drafts an acknowledgement email** — a compliant acknowledgement for the requester, pre-populated with a reference number placeholder and response deadline

---

## Why this matters

UK public authorities receive large volumes of FOI and EIR requests. The triage stage — validating, classifying, routing, and acknowledging — consumes significant FOI coordinator time before any information search has begun. Misclassifying FOIA as EIR (or vice versa) is a compliance failure with real ICO consequences. Identifying a potential S.40 (personal data) or S.43 (commercial interests) issue at triage rather than on day 18 prevents late scrambles for legal advice and missed deadlines.

This flow gives coordinators a structured, consistent starting point for every request — reducing time on initial processing and surfacing the right questions early.

---

## Inputs

| Input | Type | Required | Notes |
|---|---|---|---|
| Request text | String | Yes | Paste the full text of the FOI or EIR request as received |
| Council name | String | No | Set in Flow State on Start node — defaults to `YOUR_COUNCIL_NAME` |
| FOI team email | String | No | Set in Flow State on Start node |
| Received date | String | No | Include in pasted request text or set in Flow State |

---

## Outputs

| Output | Description |
|---|---|
| Triage report | Structured internal report: regime, summary, request breakdown, routing, complexity, flags, exemptions, deadline |
| Draft acknowledgement | Compliant email for the requester — must be reviewed before sending |
| Out-of-scope response | If the input is not a valid FOI/EIR request: what it is, why it is out of scope, suggested handling |

---

## Applicable legislation and guidance

- [Freedom of Information Act 2000](https://www.legislation.gov.uk/ukpga/2000/36/contents)
- [Environmental Information Regulations 2004](https://www.legislation.gov.uk/uksi/2004/3391/contents/made)
- [ICO guidance — Freedom of Information](https://ico.org.uk/for-organisations/foi/)
- [ICO guidance — Environmental Information Regulations](https://ico.org.uk/for-organisations/environmental-information-regulations/)
- [MoJ Section 45 Code of Practice](https://www.gov.uk/government/publications/freedom-of-information-code-of-practice)
- [ICO Decision Notices](https://icosearch.ico.org.uk/s/search.html?collection=ico-meta&profile=decisions&query=)

---

## Sector applicability

| Sector | Notes |
|---|---|
| Local government | Primary target. All UK local authorities are subject to FOIA and EIR. |
| NHS trusts | Subject to FOIA. Adapt service area routing for NHS service structure. |
| Central government | Departments and agencies subject to FOIA. S.35 (policy formulation) is relevant for central government but not currently in scope for this flow. |
| Police | Subject to FOIA. Additional S.31 (law enforcement) considerations apply. |

**Note for Scottish public authorities:** Scotland operates under the Freedom of Information (Scotland) Act 2002 (FOISA) and the Environmental Information (Scotland) Regulations 2004 (EISR). This flow is based on FOIA and EIR — Scottish authorities should treat outputs as a starting point and verify against FOISA/EISR.

---

## Governance

This flow assists trained FOI coordinators. It does not make decisions and does not send correspondence automatically.

- **Human review is mandatory** before acting on any output
- **Exemption flags are advisory** — they do not constitute a determination that any exemption applies
- **The draft acknowledgement must be reviewed** before sending — verify the reference number, deadline date, and council name
- **Section 14 and Section 12 determinations** require officer judgment — AI flags are a prompt to consider, not a decision

See [docs/governance.md](../../docs/governance.md) for the minimum governance steps required before deploying any flow in a live environment.

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- Regime classification (FOIA vs EIR) is based on the request text as written. Ambiguous requests require officer judgment.
- Exemption flagging is based on the request subject — the flow has not seen the information held. Exemption decisions must be made after information is located.
- Deadline calculation does not automatically exclude UK bank holidays — coordinators must verify.
- Not designed for Scottish FOI legislation (FOISA/EISR).
- Vexatious requests are flagged and passed through for full triage — they are valid FOI requests and must be formally refused by an officer, not ignored.
