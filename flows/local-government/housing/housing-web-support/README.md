# Housing & Homelessness Web Support Assistant

Resident-facing web chat assistant for housing and homelessness queries. Helps residents understand the housing register, report repairs, access homelessness advice, and navigate housing options.

---

## What it does

A single-agent Flowise agentflow designed to be embedded as a web chat widget on the housing section of a council website. The agent:

1. Receives a resident's free-text query via the chat interface
2. Searches the connected RAG vector store (council housing web pages + government guidance)
3. Returns a plain-English answer, signposting to the relevant service page, form, or contact
4. Escalates to a human contact when the query requires it — homelessness tonight, domestic abuse, rough sleeping, imminent eviction, or complex eligibility questions

---

## Who it is for

- Residents visiting the housing section of the council website
- Council housing and digital teams deploying resident-facing AI

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
| `service_phone` | Housing duty contact number, including out-of-hours emergency homelessness line | `020 7525 0000` |
| `service_email` | Housing team email address | `housing@yourcouncil.gov.uk` |
| `service_url` | URL of the council's housing web pages or housing portal | `https://www.yourcouncil.gov.uk/housing` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

> **Important:** The `service_phone` for this flow should include an out-of-hours emergency number for homelessness. Residents presenting as homeless at night must be able to reach someone — this number must be staffed 24/7.

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council housing web pages | Council website | Ingest all pages under `/housing`, `/homelessness`, `/repairs`. Refresh monthly. |
| Housing allocations policy | Council document | The allocations policy determines eligibility and priority banding. Varies significantly between councils — ingest the current adopted policy. |
| Choice-based lettings scheme rules | Council document | Ingest the CBL scheme rules including bidding process, property types, and eligibility. |
| Homelessness strategy | Council document | Ingest the current homelessness and rough sleeping strategy for local context. |
| Repair reporting guidance | Council website | Include categories of repair, how to report, response time targets, and emergency repair process. |
| Right to Buy guidance | Council website | Include eligibility criteria, discount amounts, and application process. |
| GOV.UK — Council housing | Public guidance | [gov.uk/council-housing](https://www.gov.uk/council-housing) |
| GOV.UK — Homelessness | Public guidance | [gov.uk/homelessness-help-from-council](https://www.gov.uk/homelessness-help-from-council) |
| GOV.UK — Right to Buy | Public guidance | [gov.uk/right-to-buy-buying-your-council-home](https://www.gov.uk/right-to-buy-buying-your-council-home) |
| Shelter — Housing advice | Public guidance | [shelter.org.uk/housing_advice](https://england.shelter.org.uk/housing_advice) — general housing rights for private tenants |

### Refresh cadence

- **Monthly** — general service pages
- **Immediately** — when the allocations policy, CBL scheme rules, or homelessness duty process changes
- **When published** — when a new homelessness strategy is adopted

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| Housing portal (Locata, Jigsaw, Home Connections) | Council-specific | Deep-link to the CBL portal for bidding and application status. |
| Repair reporting system (NEC Housing, MRI, Civica) | Council-specific | If the repair system has an API, surface repair booking in chat. Otherwise link to the online reporting form. |
| StreetLink | Public | [streetlink.org.uk](https://www.streetlink.org.uk) — direct residents here to refer a rough sleeper. No API integration needed. |
| Shelter helpline | External | 0808 800 4444 — free housing advice. Mention for residents at risk of homelessness or with complex private tenancy issues. |
| Refuge — domestic abuse | External | 0808 2000 247 — mention for residents fleeing domestic abuse who need housing help. Do not attempt to handle these queries in the chat beyond immediate signposting. |
| Housing management system (NEC, Civica, MRI) | Write-access / proprietary | Application and tenancy data is held in the HMS. Councils must implement a secure API wrapper for any account-specific responses — never expose case data in chat. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `medium` |
| Data sensitivity | `medium` |
| Human review required | Yes |

Review conversation logs at least weekly. Any conversation involving domestic abuse, rough sleeping, or imminent homelessness must be flagged for the housing team to follow up — do not rely on the assistant to fully resolve these cases.

Residents may share sensitive personal circumstances including homelessness, domestic abuse, and mental health issues. The assistant must handle these with care and always prioritise signposting to human support over attempting to resolve complex cases via chat.

See [docs/governance.md](../../../../docs/governance.md) for the minimum governance steps before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- Housing Act 1996
- Homelessness Reduction Act 2017
- Housing Act 1985 (Right to Buy)
- Landlord and Tenant Act 1985
- [GOV.UK — Homelessness guidance](https://www.gov.uk/homelessness-help-from-council)
- [GOV.UK — Right to Buy](https://www.gov.uk/right-to-buy-buying-your-council-home)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- The assistant cannot access live housing application data — register position, bidding history, or repair status are always redirected to the housing portal or the housing team.
- Homelessness duty assessments are a statutory function requiring a qualified officer. The assistant provides information and signposting only — it must never attempt to assess whether a duty is owed.
- Domestic abuse disclosures must be escalated immediately. The assistant is not equipped to handle safeguarding in a chat context.
- Allocations and eligibility are highly policy-specific. Even with the policy loaded into the RAG store, complex eligibility queries should always be referred to a housing officer.
- Not suitable for formal complaints about housing services — the assistant can signpost to the complaints process but cannot log or process a complaint.
