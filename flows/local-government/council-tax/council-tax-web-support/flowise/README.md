# Council Tax Web Support Assistant — Flowise

Flowise implementation of the [Council Tax Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

---

## Requirements

- Flowise >= 3.0.0 (tested on 3.0.5)
- OpenAI API key
- Model: `gpt-4o-mini` at temperature `0.1`
- A populated RAG vector store — see [data sources](../README.md#data-sources--what-to-load-into-the-rag-vector-store)

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
| `service_url` | Your council tax web pages or My Account portal URL |
| `service_phone` | Your revenues team contact number |
| `service_email` | Your revenues team email address |

7. Save and test with sample queries before deploying

---

## Agent architecture

Single agent. Start node feeds directly into the Council Tax Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| Council Tax Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "how do I apply for that?" after asking about the single person discount). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing and configuring, embed the chat widget on your council website via Flowise's built-in embed options:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your council tax web pages

Recommended placement: visible on all pages under `/council-tax`, triggered by a "Chat with us" button rather than auto-opening.

---

## Testing

Before going live, test the following query types:

| Query | Expected behaviour |
|---|---|
| "I don't understand my council tax bill" | Explains bill structure from RAG content; signposts to `service_url` for account details |
| "Can I get a discount living alone?" | Explains single person discount criteria and how to apply |
| "I'm a student, do I pay council tax?" | Explains student exemption criteria from RAG content |
| "I can't afford to pay my council tax" | Empathetic response; explains CTR and hardship fund; provides `service_phone`; mentions Citizens Advice |
| "I've received a summons" | Urgent escalation to `service_phone`; advises calling today |
| "How do I set up a direct debit?" | Explains process and links to `service_url` |
| "What band is my property?" | Explains how to check via VOA; links to voa.service.gov.uk |
| "What's my current balance?" | Redirects to My Account portal at `service_url` — does not attempt to answer |
| "What's the planning application process?" | Out-of-scope redirect to `council_website` |

---

## Known limitations

- The assistant cannot access live account data — balance, payment history, and bill details are always redirected to the My Account portal
- The assistant cannot process payments, set up direct debits, or submit discount applications on behalf of a resident
- CTR eligibility is complex and council-specific — the assistant explains general criteria but always directs residents to apply via the portal rather than assessing eligibility
- Enforcement situations (bailiffs, liability orders) are escalated immediately to the revenues team — the assistant does not attempt to advise on these in detail

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
