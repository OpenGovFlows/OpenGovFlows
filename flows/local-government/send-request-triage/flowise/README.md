# SEND Request Triage — Flowise

Flowise implementation of the [SEND Request Triage](../README.md) flow. Built as a sequential agentflow on Flowise 3.x with four agents: Gate Check, Request Classifier, Compliance Checker, and Report Assembler.

## SPECIAL CATEGORY DATA

DO NOT use this flow in production without speakting to your Compliance team or DPO. 

This flow is designed to be used with test data only.

---

## Requirements

- Flowise >= 3.0.0 (tested on 3.0.5)
- OpenAI API key (development and testing only — see [LLM provider guidance](#llm-provider-guidance))
- Model: `gpt-4o-mini` (default across all four nodes)

---

## Setup

1. In Flowise, go to **Agentflows → Add New**
2. Click the settings icon (top right) and choose **Import**
3. Upload `workflow.json`
4. Open the **Start** node and update the Flow State values:

| Key | Default | Description |
|---|---|---|
| `council_name` | `YOUR_COUNCIL_NAME` | Your authority's full name — appears in draft acknowledgements |
| `send_team_email` | `send@yourcouncil.gov.uk` | SEND team contact email — included in acknowledgements |
| `send_team_phone` | *(blank)* | SEND team phone number — optional |
| `received_date` | *(blank)* | Date the contact was received — used for deadline calculations. Can also be included in the pasted contact text |

5. Connect your OpenAI credential to each of the four agent nodes
6. Save and test with the sample inputs below before deploying

---

## Agent architecture

```
[Start] → [Gate Check] → [Request Classifier] → [Compliance Checker] → [Report Assembler]
```

| Agent | Model | Returns as | Purpose |
|---|---|---|---|
| Gate Check | gpt-4o-mini | `assistantMessage` | Validates input; detects safeguarding; classifies contact type |
| Request Classifier | gpt-4o-mini | `assistantMessage` | Extracts identifiers; calculates statutory clock; assesses urgency |
| Compliance Checker | gpt-4o-mini | `assistantMessage` | Assesses deadline risk; checks escalation triggers; identifies gaps |
| Report Assembler | gpt-4o-mini | `userMessage` | Produces triage note and draft acknowledgement |

Agents 1–3 return as `assistantMessage` so their findings accumulate in shared conversation memory for the Report Assembler. All agents run at `temperature: 0.1`. Ephemeral memory is enabled — each session starts fresh.

---

## Usage

Paste the full text of an incoming SEND contact into the chat input. Include the sender's name, role, date, and contact details where available:

---

## What the flow produces

**For valid SEND contacts**, the flow returns two outputs in a single response:

**1. SEND Triage Note** — structured internal document covering:
- Contact type and urgency rating
- Child and sender identifiers
- Plain-English summary of what is needed
- Statutory clock, deadline, and deadline risk rating
- Seven escalation flags (exclusion, placement breakdown, child not in education, tribunal/mediation, transition at risk, family distress, missed previous deadline)
- Routing recommendation
- Information gaps
- Data handling note
- AI disclaimer

**2. Draft Acknowledgement** — ready to review and send, with:
- Tone adapted to sender type (warm for families, formal for professionals)
- Statutory timeline stated in general terms for statutory contact types
- Council name and contact details from Flow State

**For out-of-scope contacts**, the flow returns a structured `OUT_OF_SCOPE` block with what was received, why it is out of scope, and where to route it.

**For safeguarding concerns**, the flow immediately returns a `SAFEGUARDING_REFERRAL` notice and stops. No triage note or acknowledgement is produced.

---

## LLM provider guidance

**Development and testing:** the workflow imports with OpenAI `gpt-4o-mini` configured. This flow handles special category personal data — children's health, educational, and care records. Do not use OpenAI with real case data without DPO sign-off and a reviewed data processing agreement covering special category data under UK GDPR Article 9.

**Production:** switch to Amazon Bedrock. Bedrock provides data residency controls and enterprise DPAs suitable for UK public sector. To switch, open each agent node, change the model to your Bedrock credential, and select the appropriate Claude model.

---

## Known limitations

- The flow has no access to your case management system — check child records in your CMS before acting on any triage output
- **Transfer-of-plan clock:** the 15-week clock runs from the date the child moves into the area, not the date the contact is received. The flow uses the contact date as a proxy when the move date is not stated — coordinators must confirm the actual move date and recalculate
- **Transfer-of-plan deadline arithmetic** is particularly prone to error — always verify independently
- Deadline calculations do not account for bank holidays or school holidays
- The draft acknowledgement must be reviewed before sending — do not automate dispatch
- Vexatious or abusive contacts from families are still in-scope SEND contacts if they reference EHC provision — the flow will triage them and coordinators should handle them through normal channels

---

## Governance requirements

Before deploying in a live environment:

- Complete an AI risk assessment — see [docs/governance.md](../../../../docs/governance.md)
- Involve your DPO — this flow processes special category personal data and a DPIA is likely required
- Confirm your LLM provider's data processing agreement covers special category data for children
- Document who reviews outputs before any action is taken
- Establish that no acknowledgement will be sent without human review
- Ensure coordinators understand that all escalation flags are advisory only

---

## Cost estimate

| Model | Typical contact (300 words) | Estimated cost per run |
|---|---|---|
| gpt-4o-mini | ~2,500 tokens in, ~2,000 tokens out across 4 agents | ~$0.003 |
| gpt-4o | ~2,500 tokens in, ~2,000 tokens out across 4 agents | ~$0.05 |

Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
