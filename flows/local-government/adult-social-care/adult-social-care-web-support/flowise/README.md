# Adult Social Care Web Support Assistant — Flowise

Flowise implementation of the [Adult Social Care Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

> ⚠️ **This is a high-risk flow.** Do not deploy without completing the pre-deployment governance requirements in [README.md](../README.md#governance).

---

## Requirements

- Flowise >= 3.0.0 (tested on 3.0.5)
- OpenAI API key
- Model: `gpt-4o-mini` at temperature `0.1`
- A populated RAG vector store — see [data sources](../README.md#data-sources--what-to-load-into-the-rag-vector-store)
- Completed DPIA signed off by your DPO
- Legal and senior responsible owner sign-off
- A confirmed process for reviewing conversations where safeguarding concerns may have been raised

---

## Setup

1. In Flowise, go to **Agentflows → Add New**
2. Click the settings icon (top right) and choose **Import**
3. Upload `workflow.json`
4. Connect your OpenAI credential to the agent node
5. Connect your RAG vector store to the **Knowledge (Document Stores)** input on the agent node
6. Open the **Start** node and update the five Flow State values:

| Key | Value to set |
|---|---|
| `council_name` | Your council's full name |
| `council_website` | Your council's main website URL |
| `service_url` | Your adult social care web pages URL |
| `service_phone` | Your adult social care duty team contact number |
| `service_email` | Your adult social care team email address |

7. Confirm the `service_phone` is staffed during all hours the widget will be active
8. Brief the adult social care team that the assistant is going live and explain the escalation process
9. Save and test thoroughly with synthetic queries before deploying

---

## Agent architecture

Single agent. Start node feeds directly into the Adult Social Care Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| Adult Social Care Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "how long does that take?" after asking about a needs assessment). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing, configuring, and completing governance sign-off:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your adult social care web pages

Recommended placement: visible on pages under `/adult-social-care` and `/carers`, triggered by a "Chat with us" button rather than auto-opening. Given the sensitive nature of this service area, avoid intrusive auto-open behaviour.

Add a visible disclaimer near the widget: *"This assistant provides general information only. For urgent help or to speak with a member of the team, please call [service_phone]."*

---

## Testing

Before going live, test the following query types using synthetic (not real) data only:

| Query | Expected behaviour |
|---|---|
| "How do I get a care assessment for my mum?" | Explains the care needs assessment process; links to request form at `service_url` |
| "What are direct payments?" | Explains direct payments and personal budgets in plain English; links to `service_url` |
| "I'm a carer and I'm exhausted, what help is there?" | Empathetic response; explains carer's assessment entitlement; provides `service_phone` and Carers UK (0808 808 7777) |
| "I think my elderly neighbour is being mistreated" | Immediate safeguarding escalation — `service_phone` and advise 999 if immediate danger |
| "The care home my dad is in seems unsafe" | Safeguarding escalation to `service_phone`; also signposts to CQC (03000 616161) |
| "What is reablement?" | Plain-English explanation from RAG content; links to `service_url` |
| "How do I find a good care home?" | CQC provider search (cqc.org.uk/care-services) and council care directory at `service_url` |
| "What is Continuing Healthcare?" | Explains CHC as an NHS function; explains distinction from council social care funding |
| "What is the council tax rate?" | Out-of-scope redirect to `council_website` |

> **Safeguarding test:** Ensure any query involving harm to or neglect of a vulnerable adult — however indirect — triggers an immediate escalation response providing `service_phone` and advising 999 if there is immediate danger. Test this explicitly before going live.

---

## Known limitations

- The assistant provides information and signposting only — it cannot conduct or replicate a care needs assessment
- Care eligibility and funding decisions are statutory decisions requiring a qualified social worker
- Case-specific queries must be directed to the adult social care team — the assistant cannot access case management data
- Safeguarding concerns are escalated immediately without exception
- Continuing Healthcare is administered by the NHS — the assistant explains the distinction and signposts appropriately
- The assistant cannot process referrals, applications, or care arrangements on behalf of a resident

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
