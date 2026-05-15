# Planning & Development Web Support Assistant

Resident and applicant-facing web chat assistant for planning queries. Helps residents and applicants search and comment on planning applications, understand permitted development rights, and navigate the planning system.

---

## What it does

A single-agent Flowise agentflow designed to be embedded as a web chat widget on the planning section of a council website. The agent:

1. Receives a resident's or applicant's free-text query via the chat interface
2. Searches the connected RAG vector store (council planning pages + national planning guidance)
3. Returns a plain-English answer, signposting to the public register, relevant guidance, or a council contact
4. Escalates to a human contact when the query requires it — specific project advice, enforcement complaints, listed building works, or planning appeals

---

## Who it is for

- Residents and applicants visiting the planning section of the council website
- Businesses and developers seeking general guidance before submitting an application
- Council planning and digital teams deploying resident-facing AI

---

## Inputs

| Input | Type | Notes |
|---|---|---|
| Resident or applicant query | Free text | Entered via the embedded web chat widget |

## Outputs

| Output | Description |
|---|---|
| Assistant response | Plain-English answer, signpost to the public register or guidance, or escalation to a planning officer |

---

## Flow State — configure before deploying

Open the Start node in Flowise and update these values before going live:

| Key | Description | Example |
|---|---|---|
| `council_name` | Your council's full name | `London Borough of Southwark` |
| `service_phone` | Planning enquiries contact number | `020 7525 0000` |
| `service_email` | Planning enquiries email address | `planning@yourcouncil.gov.uk` |
| `service_url` | URL of the council's planning web pages or public access planning register | `https://www.yourcouncil.gov.uk/planning` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council planning web pages | Council website | Ingest all pages under `/planning`. Refresh quarterly or when key policies change. |
| Council Local Plan | Council document | Ingest the adopted local plan. Update when a new plan is adopted or significant modifications are made. |
| Statement of Community Involvement | Council document | Explains how residents can engage with the planning process — useful for comment and consultation queries. |
| Pre-application advice service details | Council website | Include what pre-app advice covers, how to request it, fees, and timescales. |
| Conservation area information | Council website | Include which areas are designated, what additional controls apply, and how to find out if a property is affected. |
| GOV.UK — Planning permission | Public guidance | [gov.uk/planning-permission-england-wales](https://www.gov.uk/planning-permission-england-wales) |
| GOV.UK — Permitted development for householders | Public guidance | [gov.uk/government/publications/permitted-development-rights-for-householders-technical-guidance](https://www.gov.uk/government/publications/permitted-development-rights-for-householders-technical-guidance) |
| Planning Portal — Common projects | Public guidance | [planningportal.co.uk/permission/common-projects](https://www.planningportal.co.uk/permission/common-projects) — covers extensions, outbuildings, loft conversions, etc. |
| National Planning Policy Framework (NPPF) | Public guidance | [gov.uk/government/publications/national-planning-policy-framework--2](https://www.gov.uk/government/publications/national-planning-policy-framework--2) — ingest as background; not needed for most resident queries. |

### Refresh cadence

- **Quarterly** — general planning service pages
- **Immediately** — when the local plan is adopted or significantly modified
- **When updated** — when national permitted development rules change (typically via secondary legislation)

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| Public access planning register (Idox/Uniform, MasterGov, Northgate) | Council-specific | Idox Public Access has an API. Surfacing live application status and address-based search in chat is the single highest-value integration for this flow — "what's happening with the application at [address]?" is the most common planning query. |
| Planning Portal | Public | [planningportal.co.uk](https://www.planningportal.co.uk) — link for application submission guidance and the common projects tool. |
| Pre-application advice booking form | Council-specific | Link directly to the pre-app service booking page. |
| Planning Inspectorate — appeals | Public | [gov.uk/appeal-planning-decision](https://www.gov.uk/appeal-planning-decision) — link for residents who have received a refusal and want to appeal. |
| Enforcement complaint form | Council-specific | Link directly to the online enforcement complaint form. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `medium` |
| Data sensitivity | `low` |
| Human review required | Yes |

Review conversation logs monthly. Flag patterns indicating residents are confused about the difference between planning permission and building regulations, or are receiving inconsistent guidance on permitted development — these are signals to improve the RAG content or system prompt.

The assistant must never give a view on whether a specific proposal will receive planning permission, and must always recommend professional advice for complex or listed building queries. Works to listed buildings without consent can result in criminal prosecution — the stakes of incorrect guidance are high.

See [docs/governance.md](../../../../docs/governance.md) for the minimum governance steps before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- Town and Country Planning Act 1990
- Planning (Listed Buildings and Conservation Areas) Act 1990
- Town and Country Planning (Development Management Procedure) (England) Order 2015
- Town and Country Planning (General Permitted Development) (England) Order 2015
- National Planning Policy Framework (NPPF)
- [Planning Portal](https://www.planningportal.co.uk)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- The assistant provides general guidance only — it cannot determine whether a specific proposal requires planning permission or will be approved. Always recommend professional advice.
- Permitted development rules are complex and have many exceptions (Article 4 directions, listed buildings, flats, commercial premises). The assistant can outline general rules but should always caveat that local restrictions may apply.
- Without a live planning register integration, the assistant cannot look up specific application statuses — it directs residents to the public access register.
- The assistant cannot submit applications, comments, or enforcement complaints on behalf of a user.
- Building regulations queries are in scope for general explanation only — detailed building regulations advice requires a building control officer or approved inspector.
