# SEND & Children's Services Web Support Assistant

Resident and family-facing web chat assistant for SEND and children's services queries. Helps families understand the EHC plan process, navigate the local offer, access early help, and find children's services support.

> ⚠️ **This is a high-risk flow.** It involves children and families, some of whom may be in vulnerable situations. Additional governance steps are required before deployment. See the Governance section below.

---

## What it does

A single-agent flow designed to be embedded as a web chat widget on the SEND and children's services section of a council website. The agent:

1. Receives a family's or resident's free-text query via the chat interface
2. Searches the connected RAG vector store (council SEND pages + SEND Code of Practice guidance)
3. Returns a plain-English answer, signposting to the Local Offer, SENDIASS, or relevant service contact
4. Escalates immediately and without exception when a safeguarding concern is raised

---

## Who it is for

- Parents, carers, and young people visiting the SEND section of the council website
- Families navigating the EHC plan process or looking for local support
- Council SEND, children's services, and digital teams deploying resident-facing AI

---

## Inputs

| Input | Type | Notes |
|---|---|---|
| Family or resident query | Free text | Entered via the embedded web chat widget |

## Outputs

| Output | Description |
|---|---|
| Assistant response | Plain-English answer, signpost to the Local Offer or SENDIASS, or immediate escalation when safeguarding concerns are raised |

---

## Flow State — configure before deploying

Open the Start node in Flowise and update these values before going live:

| Key | Description | Example |
|---|---|---|
| `council_name` | Your council's full name | `London Borough of Southwark` |
| `service_phone` | SEND and children's services main contact number, including the children's services duty team number | `020 7525 0000` |
| `service_email` | SEND team email address | `send@yourcouncil.gov.uk` |
| `service_url` | URL of the council's SEND web pages or Local Offer | `https://www.yourcouncil.gov.uk/send` |
| `council_website` | Main council website URL — used when redirecting out-of-scope queries | `https://www.yourcouncil.gov.uk` |

> **Important:** The `service_phone` must include the children's services duty team number. This is the number provided when safeguarding concerns are raised — it must be staffed during all hours the chat widget is active, and ideally 24/7.

---

## Data sources — what to load into the RAG vector store

The agent's knowledge comes from a vector store you populate and maintain. The quality of responses depends directly on the quality and currency of this content.

### Recommended sources

| Source | Type | Notes |
|---|---|---|
| Council SEND web pages and Local Offer | Council website | Ingest all pages under `/send`, `/local-offer`, `/children-with-disabilities`. Refresh monthly. |
| EHCP request and process guidance | Council website | Include how to request an assessment, the 20-week statutory timeline, and what happens at each stage. |
| School admissions guidance for SEND | Council website | Include the process for naming a school in an EHCP and the role of the SEND team in admissions. |
| Early help and family support services | Council website | Include what early help is, how to access it, and what services are available locally. |
| SENDIASS service details | Council website | Include contact details, what SENDIASS can help with, and how to access the service. |
| SEND Code of Practice 2015 | Public guidance | [gov.uk/government/publications/send-code-of-practice-0-to-25](https://www.gov.uk/government/publications/send-code-of-practice-0-to-25) — the statutory framework for SEND support. |
| GOV.UK — Children with special educational needs and disabilities | Public guidance | [gov.uk/children-with-special-educational-needs](https://www.gov.uk/children-with-special-educational-needs) |
| SEND Tribunal guidance | Public guidance | [gov.uk/appeal-special-educational-needs-decision](https://www.gov.uk/appeal-special-educational-needs-decision) |

### Refresh cadence

- **Monthly** — general SEND service pages and Local Offer
- **Immediately** — when EHCP timescales, referral processes, or local SENDIASS contact details change
- **When published** — when the Local Offer is reviewed or updated annually

---

## Integration patterns

| Integration | Type | Notes |
|---|---|---|
| Local Offer website | Council-specific | Many councils host the Local Offer as a separate site. Deep-link to specific sections where possible. |
| EHCP request form | Council-specific | Link directly to the EHC needs assessment request form. |
| SEND case management system (Capita ONE, EHC Hub, Liquidlogic) | Write-access / proprietary | EHCP status, annual review dates, and placement information are held in the SEND case management system. Councils must implement a secure API wrapper for any case-specific responses — never expose case data in chat. |
| SENDIASS referral | Council-specific | Link directly to the local SENDIASS referral or contact page. |
| Contact (national charity) | Public | [contact.org.uk](https://contact.org.uk) — national charity supporting families with disabled children. Useful general signpost. |

---

## Governance

| Field | Value |
|---|---|
| Risk level | `high` |
| Data sensitivity | `high` |
| Human review required | Yes — see notes below |

### Human review requirements

All conversations must be available for review. This flow involves children and families, some of whom may be in vulnerable situations.

- **Safeguarding**: Any conversation where a safeguarding concern may have been raised — however indirectly — must be reviewed by a qualified social worker. Establish a clear process for flagging these conversations before going live.
- **Weekly review**: A named officer in the SEND team should review a sample of conversations weekly to ensure the assistant is providing accurate guidance and appropriate escalations.
- **Escalation log**: Maintain a log of all conversations where the assistant escalated to the duty team or NSPCC. Review monthly.

### Pre-deployment requirements

Before going live with this flow, the following must be in place:

1. A full Data Protection Impact Assessment (DPIA) signed off by your DPO
2. Legal sign-off confirming the flow does not conflict with statutory SEND duties
3. Sign-off from the Head of SEND or equivalent senior responsible owner
4. A clear process for reviewing conversations where safeguarding concerns may have been raised
5. Confirmation that the `service_phone` is staffed during all hours the widget is active
6. Staff awareness — the SEND team must know the assistant is live and understand the escalation process

See [docs/governance.md](../../../../docs/governance.md) for the full governance framework.

---

## Applicable legislation and guidance

- Children and Families Act 2014
- SEND Code of Practice 0 to 25 (2015)
- Equality Act 2010
- Children Act 1989 and 2004 (safeguarding duties)
- [GOV.UK — SEND](https://www.gov.uk/children-with-special-educational-needs)
- [SEND Tribunal](https://www.gov.uk/appeal-special-educational-needs-decision)

---

## Platform implementations

| Platform | Status | Folder |
|---|---|---|
| Flowise | Available | [`flowise/`](./flowise/) |

---

## Limitations

- The assistant provides information and signposting only — it cannot conduct or replicate a professional SEND assessment.
- EHC plan eligibility is a statutory decision for the local authority — the assistant explains the process and criteria but does not make determinations.
- Case-specific queries (a specific child's EHCP, placement, or annual review) must be directed to the SEND team — the assistant cannot access case management data.
- Safeguarding concerns are escalated immediately without exception — the assistant is not equipped to handle these in a chat context.
- SEND Tribunal cases involve complex legal and educational arguments — families should always be directed to SENDIASS or a specialist advocate, not the assistant.
- This flow must not be deployed without completing the pre-deployment governance requirements listed above.
