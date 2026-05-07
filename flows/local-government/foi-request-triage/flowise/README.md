# FOI Request Triage — Flowise

Flowise implementation of the [FOI Request Triage](../README.md) flow. Built as a sequential agentflow on Flowise 3.x with four agents: Gate Check, Request Analyser, Exemption Flagger, and Report Assembler.

---

## Requirements

- Flowise >= 3.0.0 (tested on 3.0.5)
- OpenAI API key
- Model: `gpt-4o-mini` (default) — see [cost estimate](#cost-estimate) and [model guidance](#model-guidance) below

---

## Setup

1. In Flowise, go to Agentflows → Add New
2. Click the settings icon (top right) and choose Import
3. Upload workflow.json
4. Connect your OpenAI credential to each of the four agent nodes (Gate Check, Request Analyser, Exemption Flagger, Report Assembler)
5. Open the Start node and update the Flow State values:

council_name → your authority's full name, e.g. London Borough of Southwark
foi_team_email → your FOI inbox, e.g. foi@southwark.gov.uk
Leave received_date blank - This is intended for future use


Save and test with sample requests before deploying

---

## Agent architecture

```
[Start] → [Gate Check] → [Request Analyser] → [Exemption Flagger] → [Report Assembler]
```

| Agent | Model | Return response as | Purpose |
|---|---|---|---|
| Gate Check | gpt-4o-mini | Assistant Message | Validates input; classifies FOIA or EIR; flags vexatious, cost limit, repeat, round-robin |
| Request Analyser | gpt-4o-mini | Assistant Message | Extracts distinct requests; routes to service areas; assesses complexity; calculates deadline |
| Exemption Flagger | gpt-4o-mini | Assistant Message | Identifies potential FOIA/EIR exemptions — advisory only |
| Report Assembler | gpt-4o-mini | User Message | Synthesises findings into triage report and draft acknowledgement email |

All agents except the Report Assembler return their response as **Assistant Message**, so findings accumulate in shared conversation history. The Report Assembler reads the full conversation and synthesises the final output.

---

## Flow State configuration

Update these values on the Start node before deploying:

| Key | Default | Description |
|---|---|---|
| `council_name` | `YOUR_COUNCIL_NAME` | Your authority's name — appears in the draft acknowledgement email |
| `foi_team_email` | `foi@yourcouncil.gov.uk` | Your FOI team's contact email |
| `received_date` | *(blank)* | Leave blank — this is intended for future use |

---

## Usage

Paste the full text of an FOI or EIR request into the chat input. Include the date received and the requester's name and email if available:

```
I am writing to request, under the Freedom of Information Act 2000, 
the following information from Anytown Council:

1. The total amount spent on agency staff in the Housing department 
   in each of the last five financial years (2019/20 to 2023/24).
2. The number of agency staff employed in the Housing department 
   at any one time as of 1 April in each of those years.

Please respond to: Jane Smith, jane.smith@example.com
Date of request: 14 May 2026
```

---

## What the flow produces

**For valid FOI/EIR requests**, the flow returns two outputs in a single response:

**1. FOI Triage Report** — structured internal document covering:
- Regulatory regime (FOIA or EIR) with reason for classification
- Plain-English subject summary
- Numbered list of distinct information requests
- Service area routing (primary and secondary)
- Complexity rating (Simple / Moderate / Complex) with rationale
- Political sensitivity flag
- Response deadline (20 working days from receipt)
- Early flags: vexatious (S.14), cost limit (S.12), repeated request, round-robin
- Potential exemptions to consider, with request-specific reasons
- Legal/DPO referral recommendation

**2. Draft acknowledgement email** — ready to review and send, with:
- Requester name (if provided in the request text)
- Reference number placeholder `[FOI/YYYY/XXXX]`
- Response deadline date
- Council name from Flow State

**For out-of-scope inputs**, the flow returns a clear explanation of what was received and what the coordinator should do instead. This covers:
- General questions and enquiries
- Complaints
- Subject access requests (SAR) under UK GDPR
- Service requests
- Unrelated inputs

---

## Out-of-scope handling

The Gate Check agent identifies and handles inputs that are not FOI or EIR requests. For example:

| Input | Response |
|---|---|
| `what's the weather` | OUT_OF_SCOPE — general enquiry |
| `I want to see all the information you hold about me` | OUT_OF_SCOPE — subject access request, route to DPO |
| `I want to complain about my bin collection` | OUT_OF_SCOPE — complaint, route to customer services |

Note: vexatious and overly broad requests are **not** out of scope. They are valid FOI requests that must be triaged and flagged — only a trained officer can formally apply Section 14. The Gate Check flags these in the Early Flags section and passes them through to full triage.

---

## Model guidance

The flow is configured to use `gpt-4o-mini` on all four nodes, which is sufficient for most requests. For production deployments handling politically sensitive, complex, or legally significant requests, consider upgrading the **Exemption Flagger** and **Report Assembler** nodes to `gpt-4o` for higher quality exemption reasoning and report synthesis.

Do not use GPT-3.5-turbo — FOIA/EIR regime classification requires reliable instruction-following.

---

## Known limitations

- **Test 4 (vexatious requests):** The Gate Check may still occasionally misclassify highly abusive or obviously disproportionate requests as out of scope rather than flagging and passing them through. If this occurs, the coordinator should process the request manually. A prompt refinement for this edge case is under development.
- FOIA/EIR regime classification is based on the request text as written. Requests that span both regimes require officer judgment.
- Exemption flagging is based on the request subject — the flow has not seen the information held. Exemption decisions must be made after information is located.
- Response deadline calculation does not automatically exclude UK bank holidays — the coordinator must verify.
- Designed for UK public authorities under FOIA 2000 and EIR 2004. Scottish authorities should verify against FOISA 2002 and EISR 2004.
- Very long requests (over ~1,500 words) may produce truncated analysis — split and run in sections.

---

## Governance requirements

Before deploying in a live environment:

- Complete an AI risk assessment (see [docs/governance.md](../../../../docs/governance.md))
- Involve your DPO — the flow processes personal data (requester details)
- Document who reviews outputs and at what stage
- Establish that no acknowledgement email will be sent without human review
- Ensure coordinators understand that exemption flags are advisory only and do not constitute legal determinations

**Do not use this flow to automatically send acknowledgement emails.** The draft must be reviewed, the reference number inserted, and the deadline verified before sending.

---

## Cost estimate

| Model | Typical request (300 words) | Estimated cost per run |
|---|---|---|
| gpt-4o-mini | ~2,000 tokens in, ~3,000 tokens out across 4 agents | ~$0.003 |
| gpt-4o | ~2,000 tokens in, ~3,000 tokens out across 4 agents | ~$0.05 |

Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.

---

## Production deployment

For production use, connect an email trigger to monitor your FOI inbox and pass incoming requests to the Flowise API endpoint:

```
POST https://your-flowise-instance/api/v1/prediction/<agentflow-id>
Content-Type: application/json

{
  "question": "[full text of FOI request]"
}
```

See the integration pattern note in `manifest.json` for the recommended n8n email trigger approach.
