# FOI & Data Protection Web Support Assistant

Resident-facing web chat assistant for FOI and data protection queries. Helps residents understand how to make Freedom of Information requests, submit subject access requests, and exercise their data rights.

---

## What it does

A single-agent flow designed to be embedded as a web chat widget on the FOI and data protection section of a council website. The agent:

1. Receives a resident's free-text query via the chat interface
2. Searches the connected RAG vector store (council FOI and data protection pages + ICO guidance)
3. Returns a plain-English answer, signposting to the correct submission channel, review process, or external regulator
4. Escalates when the query requires it — formal request submission, unhappy with a response, or a data breach concern

---

## Who it is for

- Residents visiting the FOI and data protection section of the council website
- Journalists, researchers, and businesses making information requests
- Council information governance and digital teams deploying resident-facing AI

---

## Inputs

| Input | Type | Notes |
|---|---|---|
| Resident query | Free text | Entered via the embedded web chat widget |

## Outputs

| Output | Description |
|---|---|
| Assistant response | Plain-English answer, signpost to the submission channel or ICO, or escalation guidance |

---

## Flow State — configure before deploying

Open the Start node in Flowise and update these values before going live:

| Key | Description | Example |
|---|---|---|
| `council_name` | Your council's full name | `London Borough of Southwark` |
| `service_phone` | Information governance team contact number | `020 7525 0000` |
| `service_email` | FOI and SAR submission email address | `foi@yourcouncil.gov.uk` |
| `service_url` | URL of the council's FOI and data protection web pages or online submission form | `https://www.yourcouncil.gov.uk/foi` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council FOI and data protection web pages | Council website | Ingest all pages under `/foi`, `/data-protection`, `/privacy`. Refresh quarterly or when processes change. |
| Council Publication Scheme | Council document | Ingest the FOI Publication Scheme — helps residents find already-published information before submitting a request. |
| Council privacy notices | Council document | Ingest all service-area privacy notices. Refresh when services change or new processing activities are added. |
| ICO — Your data rights | Public guidance | [ico.org.uk/for-the-public](https://ico.org.uk/for-the-public/) |
| GOV.UK — Freedom of Information | Public guidance | [gov.uk/make-a-freedom-of-information-request](https://www.gov.uk/make-a-freedom-of-information-request) |
| ICO — Guide to FOI | Public guidance | [ico.org.uk/for-organisations/foi](https://ico.org.uk/for-organisations/foi/) — useful background for answering exemption queries |

### Refresh cadence

- **Quarterly** — general service pages and privacy notices
- **Immediately** — when submission processes, contact details, or data protection policies change
- **When published** — when the council's Publication Scheme is updated

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| FOI / SAR online submission form | Council-specific | Deep-link residents to the FOI and SAR submission forms. Many councils use GovService, Granicus, or a bespoke form. |
| WhatDoTheyKnow | Public | [whatdotheyknow.com](https://www.whatdotheyknow.com) — mention as an option for residents who want their FOI request to be publicly visible. No API integration needed. |
| ICO complaint form | Public | [ico.org.uk/make-a-complaint](https://ico.org.uk/make-a-complaint/) — link for residents who have exhausted the internal review process. |
| Disclosure log | Council-specific | If the council publishes a searchable disclosure log, link to it — residents may find their answer without needing to submit a new request. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `medium` |
| Data sensitivity | `low` |
| Human review required | Yes |

Review conversation logs monthly. Flag patterns suggesting residents are being incorrectly redirected, or are confused about the difference between FOI and SAR requests. Ensure the assistant is not attempting to predict or assess whether requests will be granted.

The assistant must never attempt to process, log, or acknowledge formal FOI or SAR requests — all submissions must go through the official channel.

See [docs/governance.md](../../../../docs/governance.md) for the minimum governance steps before deploying any flow in a live environment.

---

## Applicable legislation and guidance

- Freedom of Information Act 2000
- Environmental Information Regulations 2004
- UK General Data Protection Regulation (UK GDPR)
- Data Protection Act 2018
- [ICO — Your data rights](https://ico.org.uk/for-the-public/)
- [GOV.UK — Freedom of Information](https://www.gov.uk/make-a-freedom-of-information-request)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- The assistant provides general guidance only — it cannot predict or commit to whether an FOI request will result in disclosure.
- The assistant cannot process, log, or acknowledge formal FOI or SAR requests — these must go through the official submission channel.
- Exemption queries (e.g. "why has my request been refused under section 36?") can be explained in general terms only — residents should be directed to request an internal review for case-specific guidance.
- The assistant can only assist with information held by the council. For queries about other organisations, residents are directed to the ICO.
- Environmental Information Regulations (EIR) requests follow a slightly different process to FOI — the assistant explains the general difference but directs complex EIR queries to the information governance team.
