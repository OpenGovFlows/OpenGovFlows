# Waste & Recycling Web Support Assistant — Flowise

Flowise implementation of the [Waste & Recycling Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

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
| `service_url` | Your waste and recycling web pages URL |
| `service_phone` | Your waste and recycling contact number |
| `service_email` | Your waste and recycling contact email |

7. Save and test with sample queries before deploying

---

## Agent architecture

Single agent. Start node feeds directly into the Waste & Recycling Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| Waste & Recycling Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "what about my green bin?" after asking about the grey bin). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing and configuring, embed the chat widget on your council website via Flowise's built-in embed options:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your waste and recycling web pages

Recommended placement: visible on all pages under `/waste` and `/recycling`, triggered by a "Chat with us" button rather than auto-opening.

---

## Testing

Before going live, test the following query types:

| Query | Expected behaviour |
|---|---|
| "When is my bin collected?" | Directs to collection day checker at `service_url`, or answers from RAG if schedule is loaded |
| "My bin wasn't collected yesterday" | Explains how to report a missed bin, provides link and phone number |
| "Can I put X in my recycling?" | Answers from RAG content; admits uncertainty if not in knowledge base |
| "There's a sofa dumped on my street" | Explains fly-tipping reporting; escalates if hazardous materials mentioned |
| "I need to get rid of old paint" | Hazardous waste guidance; directs to recycling centre or `service_phone` |
| "How do I complain about my bin service?" | Signposts to complaints process; does not attempt to log a complaint |
| "What's the council tax rate?" | Out-of-scope redirect to `council_website` |

---

## Known limitations

- Without a UPRN lookup tool integration, the assistant cannot retrieve a specific resident's collection day — it directs to the online checker instead
- Very localised queries (e.g. "is Anystreet on the Tuesday round?") cannot be answered without collection round data in the RAG store
- The assistant cannot submit a missed bin report on behalf of a resident — it signposts to the form or phone number
- Inputs over ~500 words may produce slower responses — the widget is designed for short conversational queries

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
