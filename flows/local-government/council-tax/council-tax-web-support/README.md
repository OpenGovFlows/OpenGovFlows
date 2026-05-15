# Council Tax Web Support Assistant

Resident-facing web chat assistant for council tax queries. Helps residents understand their bill, apply for discounts and exemptions, set up payment methods, and manage their account.

---

## What it does

A single-agent flow designed to be embedded as a web chat widget on the council tax section of a council website. The agent:

1. Receives a resident's free-text query via the chat interface
2. Searches the connected RAG vector store (council tax web pages + government guidance)
3. Returns a plain-English answer, signposting to the relevant service page, form, or contact
4. Escalates to a human contact when the query requires it — financial hardship, enforcement action, or account-specific queries

---

## Who it is for

- Residents visiting the council tax section of the council website
- Council revenues and digital teams deploying resident-facing AI

---

## Inputs

| Input | Type | Notes |
|---|---|---|
| Resident query | Free text | Entered via the embedded web chat widget |

## Outputs

| Output | Description |
|---|---|
| Assistant response | Plain-English answer, signpost to a service page or form, or escalation to a human contact |

---

## Flow State — configure before deploying

Open the Start node in Flowise and update these values before going live:

| Key | Description | Example |
|---|---|---|
| `council_name` | Your council's full name | `London Borough of Southwark` |
| `service_phone` | Revenues team contact number for council tax queries | `020 7525 0000` |
| `service_email` | Revenues team email address | `revenues@yourcouncil.gov.uk` |
| `service_url` | URL of the council's council tax web pages or My Account portal | `https://www.yourcouncil.gov.uk/council-tax` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council council tax web pages | Council website | Ingest all pages under `/council-tax`. Refresh monthly or when policy changes. |
| Local Council Tax Reduction scheme policy | Council document | The CTR scheme varies by council. Ingest the current year's approved scheme document. Refresh annually. |
| Direct debit and payment guidance | Council website | Include all payment methods, direct debit setup process, and what to do if a payment is missed. |
| Discount and exemption criteria | Council website | Single person, student, disabled person, carer, empty property, care leavers. |
| GOV.UK — Council Tax | Public guidance | [gov.uk/council-tax](https://www.gov.uk/council-tax) |
| GOV.UK — Apply for Council Tax Reduction | Public guidance | [gov.uk/apply-council-tax-reduction](https://www.gov.uk/apply-council-tax-reduction) |
| Valuation Office Agency — Check or challenge your band | Public guidance | [gov.uk/challenge-council-tax-band](https://www.gov.uk/challenge-council-tax-band) |

### Refresh cadence

- **Monthly** — general service pages
- **Immediately** — when CTR scheme criteria, discount eligibility rules, or payment processes change
- **Annually** — at the start of each financial year when the new CTR scheme is published

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| My Account portal | Council-specific | Deep-link residents to their council tax account. SSO integration allows the assistant to confirm account status without exposing data in chat. |
| Direct debit setup form | Council-specific | Link directly to the direct debit mandate form. |
| CTR application form | Council-specific | Link directly to the Council Tax Reduction application. |
| Revenues case management system (NEC, Capita, Civica) | Write-access / proprietary | Balance and payment history require integration with the revenues back-office system. Councils must implement a secure API wrapper. Never expose account data directly in chat. |
| Valuation Office Agency — band checker | Public | Direct residents to [voa.service.gov.uk](https://www.voa.service.gov.uk) to check their band. A link is sufficient — no API integration needed. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `medium` |
| Data sensitivity | `medium` |
| Human review required | Yes |

Review conversation logs weekly. Flag any conversations where residents appear to be in financial hardship or distress — ensure appropriate referrals to the hardship fund and debt advice services were made. Residents in enforcement situations (summons, bailiffs) must always be directed to call the revenues team directly.

The assistant must never ask for or record payment card numbers, bank account details, sort codes, or National Insurance numbers. All account-specific queries must be redirected to the secure My Account portal.

See [docs/governance.md](../../../../docs/governance.md) for the minimum governance steps before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- Local Government Finance Act 1992
- Council Tax (Administration and Enforcement) Regulations 1992
- Council Tax Reduction Schemes (Prescribed Requirements) (England) Regulations 2012
- [GOV.UK — Council Tax](https://www.gov.uk/council-tax)
- [GOV.UK — Council Tax Reduction](https://www.gov.uk/apply-council-tax-reduction)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- Responses are only as accurate as the content loaded into the RAG vector store. Keep the CTR scheme policy and discount criteria current.
- The assistant cannot access live account data (balance, payment history, bill details). All account-specific queries are redirected to the My Account portal or the revenues team.
- Not suitable for residents who need to discuss enforcement action in detail — always direct these residents to call the revenues team today.
- The assistant cannot process payments, set up direct debits, or submit applications on behalf of a resident.
- Not suitable for formal complaints — the assistant can signpost to the complaints process but cannot log or process a complaint.
