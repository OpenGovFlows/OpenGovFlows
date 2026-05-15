# Adult Social Care Web Support Assistant

Resident and carer-facing web chat assistant for adult social care queries. Helps residents and carers understand how to request a needs assessment, access direct payments, find care providers, and get carer support.

> ⚠️ **This is a high-risk flow.** It involves vulnerable adults who may be at risk of harm. Additional governance steps are required before deployment. See the Governance section below.

---

## What it does

A single-agent flow designed to be embedded as a web chat widget on the adult social care section of a council website. The agent:

1. Receives a resident's or carer's free-text query via the chat interface
2. Searches the connected RAG vector store (council adult social care pages + government guidance)
3. Returns a plain-English answer, signposting to the relevant service, care directory, or support organisation
4. Escalates immediately and without exception when a safeguarding concern is raised

---

## Who it is for

- Adults seeking care and support, or their families and carers
- Unpaid carers seeking a carer's assessment or support
- Council adult social care and digital teams deploying resident-facing AI

---

## Inputs

| Input | Type | Notes |
|---|---|---|
| Resident or carer query | Free text | Entered via the embedded web chat widget |

## Outputs

| Output | Description |
|---|---|
| Assistant response | Plain-English answer, signpost to the relevant service or care directory, or immediate escalation when safeguarding concerns are raised |

---

## Flow State — configure before deploying

Open the Start node in Flowise and update these values before going live:

| Key | Description | Example |
|---|---|---|
| `council_name` | Your council's full name | `London Borough of Southwark` |
| `service_phone` | Adult social care duty team contact number | `020 7525 0000` |
| `service_email` | Adult social care team email address | `adultsocialcare@yourcouncil.gov.uk` |
| `service_url` | URL of the council's adult social care web pages | `https://www.yourcouncil.gov.uk/adult-social-care` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

> **Important:** The `service_phone` must be the adult social care duty team number. This is the number provided when safeguarding concerns are raised — it must be staffed during all hours the chat widget is active, and ideally 24/7.

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council adult social care web pages | Council website | Ingest all pages under `/adult-social-care`, `/care-and-support`, `/carers`. Refresh monthly. |
| Care needs assessment process | Council website | Include how to request an assessment, what happens during one, and timescales. |
| Direct payments guidance | Council website | Include how direct payments work, who is eligible, and how to manage a personal budget. |
| Carers assessment and support services | Council website | Include what a carer's assessment involves, local carer support services, and emergency carer planning. |
| Reablement and intermediate care | Council website | Include what short-term reablement is, how it is accessed, and how it differs from long-term care. |
| Telecare and assistive technology | Council website | Include what is available, how to request an assessment, and any costs. |
| Adult safeguarding process | Council website | Include how to raise a concern, what happens next, and the six safeguarding principles. |
| GOV.UK — Care and support | Public guidance | [gov.uk/care-and-support-guide](https://www.gov.uk/care-and-support-guide) |
| GOV.UK — Carers and disability benefits | Public guidance | [gov.uk/browse/benefits/disability](https://www.gov.uk/browse/benefits/disability) |
| Care Act 2014 guidance | Public guidance | [gov.uk/government/publications/care-act-statutory-guidance](https://www.gov.uk/government/publications/care-act-statutory-guidance) |

### Refresh cadence

- **Monthly** — general adult social care service pages
- **Immediately** — when assessment processes, eligibility thresholds, or safeguarding procedures change
- **When updated** — when the care provider directory is refreshed

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| Care needs assessment request form | Council-specific | Link directly to the online self-referral or assessment request form. |
| Care provider directory | Council-specific | Many councils publish a searchable care directory. Link or surface provider searches in chat. Also available via [cqc.org.uk/care-services](https://www.cqc.org.uk/care-services). |
| CQC provider search | Public | [cqc.org.uk/care-services](https://www.cqc.org.uk/care-services) — the CQC provides an open search for regulated care providers. Surface this for residents looking for care homes or home care agencies. |
| Carers assessment referral | Council-specific | Link directly to the carer's assessment or carers support service referral form. |
| Adult social care case management (Liquidlogic, Paris, Mosaic, Controcc) | Write-access / proprietary | Assessment status, care plan, and direct payment details are held in the case management system. Councils must implement a secure API wrapper — never expose case data in chat. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `high` |
| Data sensitivity | `high` |
| Human review required | Yes — see notes below |

### Human review requirements

All conversations must be available for review. This flow involves vulnerable adults who may be at risk of harm.

- **Safeguarding**: Any conversation where a safeguarding concern may have been raised — however indirectly — must be reviewed by a qualified social worker. Establish a clear process for flagging these conversations before going live.
- **Weekly review**: A named officer in the adult social care team should review a sample of conversations weekly to ensure accurate guidance and appropriate escalations.
- **Escalation log**: Maintain a log of all conversations where the assistant escalated to the duty team. Review monthly.

### Pre-deployment requirements

Before going live with this flow, the following must be in place:

1. A full Data Protection Impact Assessment (DPIA) signed off by your DPO
2. Legal sign-off confirming the flow does not conflict with the council's Care Act 2014 duties
3. Sign-off from the Director of Adult Social Care or equivalent senior responsible owner
4. A clear process for reviewing conversations where safeguarding concerns may have been raised
5. Confirmation that the `service_phone` is staffed during all hours the widget is active
6. Staff awareness — the adult social care team must know the assistant is live and understand the escalation process

See [docs/governance.md](../../../../docs/governance.md) for the full governance framework.

---

## Applicable legislation and guidance

- Care Act 2014
- Mental Capacity Act 2005
- Care and Support Statutory Guidance (2014, updated 2023)
- Safeguarding Adults: Care and Support Statutory Guidance (Chapter 14)
- Equality Act 2010
- [GOV.UK — Care and support guide](https://www.gov.uk/care-and-support-guide)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- The assistant provides information and signposting only — it cannot conduct or replicate a care needs assessment.
- Care eligibility, care packages, and funding decisions are statutory decisions requiring a qualified social worker — the assistant explains the process but does not make determinations.
- Case-specific queries must be directed to the adult social care team — the assistant cannot access case management data.
- Safeguarding concerns are escalated immediately without exception — the assistant is not equipped to handle these in a chat context.
- Continuing Healthcare (CHC) funding is administered by the NHS, not the council — the assistant explains the distinction and signposts to the relevant NHS contact.
- This flow must not be deployed without completing the pre-deployment governance requirements listed above.
