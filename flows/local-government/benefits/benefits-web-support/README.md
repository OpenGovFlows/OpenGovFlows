# Benefits & Financial Support Web Support Assistant

Resident-facing web chat assistant for benefits and financial support queries. Helps residents understand what support is available locally and nationally, how to apply, and where to get further help.

---

## What it does

A single-agent flow designed to be embedded as a web chat widget on the benefits and financial support section of a council website. The agent:

1. Receives a resident's free-text query via the chat interface
2. Searches the connected RAG vector store (council benefits pages + government guidance)
3. Returns a plain-English answer, signposting to the relevant application, service page, or external support
4. Escalates to a human contact or specialist service when the query requires it — financial crisis, debt, or significant distress

---

## Who it is for

- Residents visiting the benefits and financial support section of the council website
- Council benefits, revenues, and digital teams deploying resident-facing AI

---

## Inputs

| Input | Type | Notes |
|---|---|---|
| Resident query | Free text | Entered via the embedded web chat widget |

## Outputs

| Output | Description |
|---|---|
| Assistant response | Plain-English answer, signpost to an application or service, or escalation to a human contact or specialist service |

---

## Flow State — configure before deploying

Open the Start node in Flowise and update these values before going live:

| Key | Description | Example |
|---|---|---|
| `council_name` | Your council's full name | `London Borough of Southwark` |
| `service_phone` | Benefits team contact number | `020 7525 0000` |
| `service_email` | Benefits team email address | `benefits@yourcouncil.gov.uk` |
| `service_url` | URL of the council's benefits web pages or application portal | `https://www.yourcouncil.gov.uk/benefits` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council benefits web pages | Council website | Ingest all pages under `/benefits`, `/financial-support`, `/housing-benefit`. Refresh monthly or when policy changes. |
| Local Council Tax Reduction scheme policy | Council document | The CTR scheme varies by council. Ingest the current year's approved scheme. Refresh annually. |
| Local welfare / hardship fund policy | Council document | Ingest the council's local welfare assistance scheme, eligibility criteria, and application process. |
| Free School Meals eligibility guidance | Council website | Include eligibility criteria and the application process. Refresh when national eligibility thresholds change. |
| Disabled Facilities Grant guidance | Council website | Include eligibility, the means test, and the application process. |
| GOV.UK — Benefits | Public guidance | [gov.uk/browse/benefits](https://www.gov.uk/browse/benefits) |
| GOV.UK — Universal Credit | Public guidance | [gov.uk/universal-credit](https://www.gov.uk/universal-credit) |
| Citizens Advice — Benefits | Public guidance | [citizensadvice.org.uk/benefits](https://www.citizensadvice.org.uk/benefits/) |

### Refresh cadence

- **Monthly** — general service pages
- **Immediately** — when CTR scheme criteria, hardship fund eligibility, or Free School Meals thresholds change
- **Annually** — at the start of each financial year when the new CTR scheme is published

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| Benefits application portal (Capita, Civica, NEC) | Council-specific | Deep-link residents directly to the benefits application portal. |
| CTR application form | Council-specific | Link directly to the Council Tax Reduction application. |
| Turn2Us benefits calculator | Public | [benefits-calculator.turn2us.org.uk](https://benefits-calculator.turn2us.org.uk) — helps residents understand entitlement before applying. Consider linking in responses. |
| Trussell Trust foodbank finder | Public | [trussell.org.uk/find-a-foodbank](https://www.trussell.org.uk/find-a-foodbank) — postcode-based foodbank finder. Use in crisis escalation responses. |
| Benefits case management system (Capita, Civica, NEC) | Write-access / proprietary | Claim status and payment history require integration with the benefits back-office system. Councils must implement a secure API wrapper. Never expose claim data in chat. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `medium` |
| Data sensitivity | `medium` |
| Human review required | Yes |

Review conversation logs weekly. Flag conversations where residents appear to be in financial crisis or significant distress, and where referrals to emergency support or debt advice may have been missed. Ensure the assistant is consistently offering appropriate signposting for residents who mention hardship, debt, or an inability to afford essentials.

The assistant must never ask for or record National Insurance numbers, bank account details, or detailed personal financial information. All applications must go through secure forms or portals.

See [docs/governance.md](../../../../docs/governance.md) for the minimum governance steps before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- Housing Benefit Regulations 2006
- Council Tax Reduction Schemes (Prescribed Requirements) (England) Regulations 2012
- Welfare Reform Act 2012
- Housing Grants, Construction and Regeneration Act 1996 (Disabled Facilities Grants)
- [GOV.UK — Benefits](https://www.gov.uk/browse/benefits)
- [GOV.UK — Universal Credit](https://www.gov.uk/universal-credit)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- The assistant cannot assess eligibility for any benefit or scheme — it explains criteria and directs residents to apply via the portal.
- Universal Credit is administered by DWP, not the council. The assistant provides general information and signposts to DWP — it cannot answer detailed UC queries.
- The assistant cannot access live claim data — status, payment dates, and amounts are always redirected to the portal or the benefits team.
- The assistant cannot process applications, payments, or discretionary decisions on behalf of a resident.
- Debt advice requires a qualified adviser — the assistant signposts to free debt advice services and does not attempt to advise on debt itself.
