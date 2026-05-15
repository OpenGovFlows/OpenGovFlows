# Environmental Health Web Support Assistant — Flowise

Flowise implementation of the [Environmental Health Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

> **Note:** The agent node in this flow is labelled "Environmental Support Assistant" in Flowise.

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
| `service_url` | Your environmental health web pages URL |
| `service_phone` | Your environmental health contact number (include out-of-hours noise line if available) |
| `service_email` | Your environmental health team email address |

7. Save and test with sample queries before deploying

---

## Agent architecture

Single agent. Start node feeds directly into the Environmental Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| Environmental Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "how long will that take?" after asking about pest control). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing and configuring, embed the chat widget on your council website via Flowise's built-in embed options:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your environmental health web pages

Recommended placement: visible on all pages under `/environmental-health`, `/noise`, and `/pest-control`, triggered by a "Chat with us" button rather than auto-opening.

---

## Testing

Before going live, test the following query types:

| Query | Expected behaviour |
|---|---|
| "My neighbour is having a loud party at 2am" | Out-of-hours noise guidance; provides `service_phone` for noise team |
| "I have rats in my garden" | Pest control service information; links to booking form at `service_url` |
| "I want to report a dirty restaurant" | Food hygiene concern reporting process; links to `service_url` and ratings.food.gov.uk |
| "What is the food hygiene rating for [restaurant]?" | FSA ratings lookup if integrated; otherwise directs to ratings.food.gov.uk |
| "My neighbour is burning rubbish and the smoke is coming into my house" | Explains statutory nuisance process for smoke; links to `service_url` |
| "There's a bad smell coming from the factory near me" | Odour nuisance process explained; `service_phone` and `service_email` provided |
| "My workplace is unsafe, who do I report it to?" | Explains HSE vs council enforcement split; directs to hse.gov.uk or 0300 003 1747 |
| "My neighbour and I don't get on, can you help?" | Acknowledges difficulty; explains environmental health can only act on statutory nuisances; suggests mediation |
| "What is the council tax rate?" | Out-of-scope redirect to `council_website` |

---

## Known limitations

- The assistant cannot guarantee a response time or outcome for environmental health complaints
- Food hygiene rating lookups require the FSA API integration — without it, residents are directed to ratings.food.gov.uk
- Workplace health and safety is largely enforced by the HSE, not the council — the assistant explains this and signposts to HSE
- The assistant cannot submit noise complaints, pest control bookings, or food hygiene reports on behalf of a resident
- Neighbour disputes involving personal conflict rather than statutory nuisance are outside the council's environmental health remit

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
