# Governance guidance

Deploying AI in UK public sector carries real responsibility. This page sets out the minimum governance steps we recommend before putting any flow from this library into live use.

This is not legal advice. It is practical guidance based on current UK public sector AI practice.

---

## Before you deploy any flow

### 1. Complete an AI risk assessment

Every AI deployment in UK public sector should be preceded by a documented risk assessment.
Your risk assessment should address:

- What decisions or actions does this AI assist with?
- What is the impact if the AI produces incorrect output?
- Who reviews AI outputs before they are acted on?
- How will you monitor for degraded performance over time?
- What is the escalation path if something goes wrong?
- How will you tell affected citizens or staff that AI is being used?

### 2. Identify a Senior Responsible Owner

Every AI deployment needs a named person accountable for it.

### 3. Involve your DPO

If the flow processes personal data — including names, addresses, case references, or anything that could identify an individual — you must involve your Data Protection Officer before deployment.

Key questions for your DPO:

- Is there a lawful basis for processing this data with an AI system?
- Does your AI provider's data processing agreement cover this use case?
- Should a Data Protection Impact Assessment (DPIA) be completed?
- Where is data processed and stored, and for how long?
- Does the use of AI constitute automated decision-making under UK GDPR Article 22?

### 4. Check your AI provider's data terms

These flows are LLM-agnostic within Flowise. Whichever provider you use, check:

- Whether your data is used for model training, and how to opt out
- Where data is processed geographically
- What their data retention policy is
- Whether their terms permit UK public sector and government use


### 5. Establish human review

No flow in this library should be deployed without human review of outputs before they are acted on.
Document who reviews what, at what frequency, and what constitutes an output that should be escalated or rejected. Include this in your risk assessment.

---

## Communicating AI use to citizens and staff

Where AI is used to assist in decisions or correspondence affecting citizens, you should consider whether and how to communicate this. The UK Government AI Playbook recommends transparency about AI use as a default position.

For staff-facing tools, ensure users understand:

- That the tool uses AI and may produce incorrect outputs
- That they remain responsible for the accuracy of any output they act on
- How to report concerns about AI behaviour

---

## Relevant frameworks and standards

| Framework | Relevance |
|-----------|-----------|
| [UK Government AI Playbook](https://www.gov.uk/government/publications/ai-playbook-for-the-uk-government) | Foundational guidance for AI in government |
| [ICO guidance on AI and data protection](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/artificial-intelligence/) | Data protection obligations when using AI |
| [ISO 42001](https://www.iso.org/standard/81230.html) | AI management system standard — flows in this library are designed to align with it |
| [DSIT AI safety guidance](https://www.gov.uk/government/collections/ai-safety) | Cross-government AI safety requirements |
| [NHS AI Lab guidance](https://www.nhsx.nhs.uk/ai-lab/) | NHS-specific AI governance |
| [Local Government Association AI guidance](https://www.local.gov.uk) | Practical guidance for council AI adoption |

---

## ISO 42001 alignment

The flows in this library are designed to support ISO 42001-aligned AI governance. Specifically:

- Each `manifest.json` includes explicit risk classification and human review requirements
- Each flow README includes a governance notes section
- Audit logging is recommended in deployment guidance
- The AI Risk Assessment Generator flow produces documentation compatible with ISO 42001 requirements

ISO 42001 does not mandate specific technical controls. It requires that you have a documented AI management system. These flows can form part of that system, but are not a substitute for it.

---

## Each flow's governance metadata

Every flow in this library includes a `governance` block in its `manifest.json`:

```json
"governance": {
  "human_review_required": true,
  "data_sensitivity": "medium",
  "risk_level": "medium",
  "iso_42001_aligned": true
}
```

`risk_level` values:

| Level | Meaning |
|-------|---------|
| `low` | Assistive only, no citizen impact, easily verifiable outputs |
| `medium` | Assists with decisions or correspondence affecting citizens |
| `high` | Assists with decisions with significant legal, clinical, or financial consequences |

All flows with `risk_level: high` require additional governance steps beyond the minimum described here. Consult your legal team and DPO before deploying.

---

## Reporting a governance concern

If you identify a flow that understates risk, produces outputs that could mislead, or has governance implications not covered in its documentation, please [open an issue](https://github.com/opengovflows/open-gov-flows/issues).
