# Planning & Development Web Support Assistant — Flowise

Flowise implementation of the [Planning & Development Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

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
| `service_url` | Your planning web pages or public access register URL |
| `service_phone` | Your planning enquiries contact number |
| `service_email` | Your planning enquiries email address |

7. Save and test with sample queries before deploying

---

## Agent architecture

Single agent. Start node feeds directly into the Planning & Development Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| Planning & Development Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "how long does that take?" after asking about the pre-application advice service). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing and configuring, embed the chat widget on your council website via Flowise's built-in embed options:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your planning web pages

Recommended placement: visible on all pages under `/planning`, triggered by a "Chat with us" button rather than auto-opening. Consider also placing on the public access planning register page — residents searching for applications often have related questions.

---

## Testing

Before going live, test the following query types:

| Query | Expected behaviour |
|---|---|
| "How do I find a planning application near me?" | Directs to public register at `service_url`; explains how to search by address |
| "How do I comment on a planning application?" | Explains the comment process including what material planning considerations are |
| "Do I need planning permission for a single storey rear extension?" | General permitted development guidance with caveat to check local restrictions; links to Planning Portal |
| "Will my loft conversion get approved?" | Declines to give a specific view; directs to pre-application advice service |
| "My neighbour has built without planning permission" | Explains enforcement complaint process; links to enforcement form at `service_url` |
| "My house is in a conservation area, can I replace my windows?" | Explains additional controls in conservation areas; strongly recommends professional advice before proceeding |
| "How do I appeal a planning refusal?" | Explains the Planning Inspectorate appeal process; links to gov.uk/appeal-planning-decision |
| "What is an Article 4 direction?" | Explains in plain English from RAG content |
| "What is the council tax rate?" | Out-of-scope redirect to `council_website` |

---

## Known limitations

- The assistant cannot look up live application statuses without a planning register API integration — it directs residents to the public access register
- Permitted development rules have many local exceptions — the assistant outlines general rules but cannot confirm whether a specific property is affected by an Article 4 direction or other restriction without that data in the RAG store
- The assistant cannot submit applications, comments, or enforcement complaints on behalf of a user
- Building regulations advice beyond general explanation requires a building control officer — the assistant signposts rather than advises in detail

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
