# Waste & Recycling Web Support Assistant

Resident-facing web chat assistant for waste and recycling queries. Helps residents find collection days, report missed bins, book bulky waste collections, and understand what can be recycled.

---

## What it does

A single-agent designed to be embedded as a web chat widget on the waste and recycling section of a council website. The agent:

1. Receives a resident's free-text query via the chat interface
2. Searches the connected RAG vector store (council waste web pages + government guidance)
3. Returns a plain-English answer, signposting to the relevant service page, form, or contact
4. Escalates to a human contact when the query requires it — hazardous fly-tipping, clinical waste, persistent service failures

---

## Who it is for

- Residents visiting the waste and recycling section of the council website
- Council digital and transformation teams deploying resident-facing AI

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
| `service_phone` | Main contact number for waste and recycling (missed bins, fly-tipping) | `020 7525 0000` |
| `service_email` | Waste and recycling service email address | `waste@yourcouncil.gov.uk` |
| `service_url` | URL of the council's waste and recycling web pages | `https://www.yourcouncil.gov.uk/waste` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council waste and recycling web pages | Council website | Ingest all pages under `/waste`, `/recycling`, `/bins`. Refresh weekly. |
| Waste collection calendar | Council document | Ingest the current year's collection calendar. Refresh immediately when schedules change. |
| Recycling centre locations and opening hours | Council document | Refresh seasonally and before bank holidays. |
| Bulky waste booking guidance | Council website | Include eligibility criteria, items accepted, costs, and booking process. |
| Garden waste subscription details | Council website | Include sign-up process, costs, and collection frequency. |
| Fly-tipping reporting guidance | Council website | Include how to report, what happens next, and what not to do. |
| GOV.UK — Recycling and waste | Public guidance | [gov.uk/browse/environment-countryside/recycling-waste-management](https://www.gov.uk/browse/environment-countryside/recycling-waste-management) |
| Recycle Now — what goes where | Public guidance | [recyclenow.com](https://www.recyclenow.com) — general recycling guidance where council-specific content does not cover the query. |

### Refresh cadence

- **Weekly** — collection day pages and recycling centre hours
- **Immediately** — when collection rounds change, new services launch, or bank holiday schedules are published
- **Annually** — full refresh at the start of the new collection year

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| Collection day checker (UPRN lookup) | Council-specific API | Most councils expose a UPRN-based collection day lookup. Connect via a custom tool node to give residents their exact next collection date without leaving the chat. Common providers: Whitespace, Bartec, NEC. |
| Missed bin report form | Council-specific | Deep-link to the missed bin reporting form with service type pre-filled where possible. |
| Bulky waste booking system | Council-specific | If an API is available, surface available slots in chat. Otherwise link to the online booking form. |
| Recycle Now locator | Public | [recyclenow.com/recycling-locator](https://www.recyclenow.com/recycling-locator) — postcode-based recycling point finder. No API key required. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `low` |
| Data sensitivity | `low` |
| Human review required | No |

No human review is required for individual responses. Monitor conversation logs weekly for patterns indicating recurring service failures, resident frustration, or queries the assistant is failing to answer — these are signals to improve the RAG content or the system prompt.

See [docs/governance.md](../../../../docs/governance.md) for the minimum governance steps before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- Environmental Protection Act 1990
- Waste (England and Wales) Regulations 2011
- Controlled Waste (England and Wales) Regulations 2012
- [GOV.UK waste and recycling guidance](https://www.gov.uk/browse/environment-countryside/recycling-waste-management)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- Responses are only as accurate as the content loaded into the RAG vector store. Keep sources current — stale collection day information is the most common failure mode for this flow.
- The assistant cannot look up a resident's specific collection day in real time unless a UPRN lookup tool integration is built. Without this, it directs residents to the collection day checker.
- The assistant should not be the sole support channel — always display a phone number and email fallback in the chat widget.
- Not suitable for handling formal complaints — the assistant can signpost to the complaints process but cannot log or process a complaint.
