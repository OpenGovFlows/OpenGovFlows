# Environmental Health Web Support Assistant

Resident-facing web chat assistant for environmental health queries. Helps residents report noise complaints, request pest control, raise food hygiene concerns, and understand environmental nuisance processes.

---

## What it does

A single-agent flow designed to be embedded as a web chat widget on the environmental health section of a council website. The agent:

1. Receives a resident's free-text query via the chat interface
2. Searches the connected RAG vector store (council environmental health pages + government guidance)
3. Returns a plain-English answer, signposting to the relevant reporting form, contact, or external body
4. Escalates to a human contact when the query requires it — out-of-hours noise emergencies, serious food hygiene concerns, large-scale pest infestations, or workplace health and safety issues

---

## Who it is for

- Residents visiting the environmental health section of the council website
- Council environmental health and digital teams deploying resident-facing AI

---

## Inputs

| Input | Type | Notes |
|---|---|---|
| Resident query | Free text | Entered via the embedded web chat widget |

## Outputs

| Output | Description |
|---|---|
| Assistant response | Plain-English answer, signpost to a reporting form or contact, or escalation to a human contact or external body |

---

## Flow State — configure before deploying

Open the Start node in Flowise and update these values before going live:

| Key | Description | Example |
|---|---|---|
| `council_name` | Your council's full name | `London Borough of Southwark` |
| `service_phone` | Environmental health team contact number, including out-of-hours noise line if available | `020 7525 0000` |
| `service_email` | Environmental health team email address | `envhealth@yourcouncil.gov.uk` |
| `service_url` | URL of the council's environmental health web pages | `https://www.yourcouncil.gov.uk/environmental-health` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

> **Important:** The `service_phone` for this flow should include an out-of-hours noise number where one exists. Out-of-hours noise complaints are one of the most common escalation triggers for this flow.

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council environmental health web pages | Council website | Ingest all pages under `/environmental-health`, `/noise`, `/pest-control`, `/food-hygiene`. Refresh monthly. |
| Noise complaint process | Council website | Include how to report, what happens after a report, what constitutes a statutory nuisance, and out-of-hours arrangements. |
| Pest control service details | Council website | Include which pests are covered, costs (if any), how to book, and what residents should do before treatment. |
| Food hygiene inspection process | Council website | Include how to report a concern, what happens next, and how ratings are assigned. |
| Smoke control zone information | Council website | Include which areas are designated smoke control zones and what fuels and appliances are permitted. |
| FSA — Food hygiene ratings | Public guidance | [ratings.food.gov.uk](https://ratings.food.gov.uk) — the FSA provides an open API for food hygiene ratings lookups. |
| GOV.UK — Statutory nuisances | Public guidance | [gov.uk/guidance/statutory-nuisances-how-councils-deal-with-them](https://www.gov.uk/guidance/statutory-nuisances-how-councils-deal-with-them) |
| HSE — Reporting concerns | Public guidance | [hse.gov.uk/contact/concerns.htm](https://www.hse.gov.uk/contact/concerns.htm) — for signposting workplace health and safety queries. |

### Refresh cadence

- **Monthly** — general service pages
- **Seasonally** — pest control guidance (rodent season, wasp season)
- **Immediately** — when noise complaint procedures or out-of-hours arrangements change

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| Noise complaint reporting form | Council-specific | Link to the online noise complaint form. Where available, link to the council's Noise App integration for evidence collection. |
| Pest control booking | Council-specific | If the council's pest control service takes online bookings, link directly to the booking form. |
| Food hygiene ratings API (FSA) | Public | The FSA provides an open API at [api.ratings.food.gov.uk](https://api.ratings.food.gov.uk) — surface food hygiene rating lookups by business name or postcode in chat. This is a high-value integration for this flow. |
| FixMyStreet | Public | [fixmystreet.com](https://www.fixmystreet.com) — for fly-tipping and street environment issues that overlap with environmental health. |
| DEFRA air quality API | Public | [api.erg.ic.ac.uk](http://api.erg.ic.ac.uk) — real-time air quality monitoring data. Surface local readings for residents with health concerns. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `low` |
| Data sensitivity | `low` |
| Human review required | No |

No human review required for individual responses. Monitor conversation logs monthly for patterns indicating recurring service issues (e.g. repeated noise complaints in one area, food hygiene concerns about a specific business) — these may warrant proactive action by the environmental health team beyond the chat service.

The assistant must not encourage residents to share personal information about their neighbours beyond what is necessary to describe the nuisance being reported.

See [docs/governance.md](../../../../docs/governance.md) for the minimum governance steps before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- Environmental Protection Act 1990 (statutory nuisances)
- Noise Act 1996
- Food Safety Act 1990
- Health and Safety at Work etc. Act 1974
- Clean Air Act 1993 (smoke control zones)
- [GOV.UK — Statutory nuisances](https://www.gov.uk/guidance/statutory-nuisances-how-councils-deal-with-them)
- [Food Standards Agency](https://www.food.gov.uk)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- The assistant cannot guarantee a response time or outcome for any environmental health complaint — these depend on officer availability and whether the issue meets the statutory nuisance threshold.
- Food hygiene rating lookups require the FSA API integration — without it, residents are directed to ratings.food.gov.uk.
- Workplace health and safety is largely enforced by the HSE, not the council — the assistant explains this distinction and signposts accordingly.
- Neighbour disputes involving personal conflict rather than statutory nuisance are outside the council's environmental health remit — the assistant acknowledges this and can signpost to mediation services.
- The assistant cannot submit formal noise complaints or pest control bookings on behalf of a resident.
