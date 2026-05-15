# Parking & Roads Web Support Assistant

Resident-facing web chat assistant for parking and roads queries. Helps residents pay or appeal penalty charge notices, apply for permits, report potholes and streetlighting faults, and understand parking restrictions.

---

## What it does

A single-agent flow designed to be embedded as a web chat widget on the parking and roads section of a council website. The agent:

1. Receives a resident's free-text query via the chat interface
2. Searches the connected RAG vector store (council parking and roads pages + government guidance)
3. Returns a plain-English answer, signposting to the relevant payment portal, form, or contact
4. Escalates to a human contact when the query requires it — dangerous road defects, complex PCN appeals, or Blue Badge eligibility

---

## Who it is for

- Residents visiting the parking and roads section of the council website
- Council parking services, highways, and digital teams deploying resident-facing AI

---

## Inputs

| Input | Type | Notes |
|---|---|---|
| Resident query | Free text | Entered via the embedded web chat widget |

## Outputs

| Output | Description |
|---|---|
| Assistant response | Plain-English answer, signpost to a payment portal, form, or contact, or escalation to a human contact |

---

## Flow State — configure before deploying

Open the Start node in Flowise and update these values before going live:

| Key | Description | Example |
|---|---|---|
| `council_name` | Your council's full name | `London Borough of Southwark` |
| `service_phone` | Parking and highways contact number, including emergency highways line if available | `020 7525 0000` |
| `service_email` | Parking appeals and highways contact email | `parking@yourcouncil.gov.uk` |
| `service_url` | URL of the council's parking and roads web pages or PCN payment portal | `https://www.yourcouncil.gov.uk/parking` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council parking and roads web pages | Council website | Ingest all pages under `/parking`, `/roads`, `/highways`. Refresh monthly. |
| Parking permit zone maps and eligibility | Council website | Include all permit zones, eligibility criteria, costs, and application process. Refresh when zones change. |
| PCN payment and appeal process | Council website | Include the full three-stage appeal process, timescales, and links to forms. |
| Road closure notices | Council website | Include current and upcoming road closures and traffic regulation orders. Refresh weekly. |
| GOV.UK — Parking fines and penalties | Public guidance | [gov.uk/parking-tickets](https://www.gov.uk/parking-tickets) |
| GOV.UK — Blue Badge scheme | Public guidance | [gov.uk/apply-blue-badge](https://www.gov.uk/apply-blue-badge) |
| PATROL — Parking adjudicators (outside London) | Public guidance | [patrol-uk.org](https://www.patrol-uk.org) — include guidance on the independent appeal process. |
| London Tribunals (London only) | Public guidance | [londontribunals.gov.uk](https://www.londontribunals.gov.uk) — include if the council is within Greater London. |

### Refresh cadence

- **Monthly** — general parking and roads pages
- **Weekly** — road closure notices and traffic regulation orders
- **Immediately** — when permit zones, restrictions, or PCN appeal processes change

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| PCN payment portal | Council-specific | Link directly to the PCN payment page. Where possible, deep-link with the PCN reference pre-filled. |
| PCN appeal / informal challenge form | Council-specific | Link directly to the informal challenge and formal representation forms. |
| Parking permit application | Council-specific | Link to the resident and visitor permit application. |
| Pothole and streetlighting reporting (FixMyStreet, Confirm, Symology) | Council-specific | FixMyStreet ([fixmystreet.com](https://www.fixmystreet.com)) is used by many councils. If the council has a direct reporting API, connect via a custom tool node to allow in-chat fault reporting. |
| PATROL adjudicator (outside London) | Public | [patrol-uk.org](https://www.patrol-uk.org) — link for residents at the independent appeal stage. |
| London Tribunals (London only) | Public | [londontribunals.gov.uk](https://www.londontribunals.gov.uk) — link for London residents at the adjudicator stage. |
| Blue Badge application | Public | [gov.uk/apply-blue-badge](https://www.gov.uk/apply-blue-badge) — link directly. No API integration needed. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `low` |
| Data sensitivity | `low` |
| Human review required | No |

No human review required for individual responses. Monitor conversation logs monthly for patterns indicating residents are having difficulty navigating the PCN appeal process, or are receiving inconsistent guidance on permit eligibility — use findings to improve RAG content and the system prompt.

See [docs/governance.md](../../../../docs/governance.md) for the minimum governance steps before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- Road Traffic Regulation Act 1984
- Traffic Management Act 2004
- Civil Enforcement of Parking Contraventions (England) General Regulations 2007
- [GOV.UK — Parking fines and penalties](https://www.gov.uk/parking-tickets)
- [GOV.UK — Blue Badge scheme](https://www.gov.uk/apply-blue-badge)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- The assistant cannot process PCN payments — residents are directed to the payment portal.
- The assistant cannot advise on whether a PCN appeal will succeed — that is a decision for the parking adjudicator.
- Without a live reporting integration, the assistant cannot confirm receipt of a pothole or streetlighting report — it directs residents to the online form or phone number.
- Blue Badge eligibility involves medical criteria assessed by a qualified officer — the assistant directs to the GOV.UK application and does not attempt to assess eligibility.
- The assistant cannot confirm whether a specific road is adopted (maintained by the council) without that data in the RAG store — it explains how to find out and provides the contact details.
