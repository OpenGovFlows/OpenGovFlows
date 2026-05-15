# FOI & Data Protection Web Support Assistant — Flowise

Flowise implementation of the [FOI & Data Protection Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

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
| `service_url` | Your FOI and data protection web pages or submission form URL |
| `service_phone` | Your information governance team contact number |
| `service_email` | Your FOI and SAR submission email address |

7. Save and test with sample queries before deploying

---

## Agent architecture

Single agent. Start node feeds directly into the FOI Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| FOI Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "how long does a review take?" after asking about the internal review process). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing and configuring, embed the chat widget on your council website via Flowise's built-in embed options:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your FOI and data protection web pages

Recommended placement: visible on all pages under `/foi` and `/data-protection`, triggered by a "Chat with us" button rather than auto-opening.

---

## Testing

Before going live, test the following query types:

| Query | Expected behaviour |
|---|---|
| "How do I make an FOI request?" | Explains the process and provides `service_email` and `service_url` |
| "What is a subject access request?" | Explains SAR in plain English, distinguishes from FOI |
| "How long does the council have to respond to my FOI?" | 20 working days, with explanation of extensions |
| "My FOI request was refused, what can I do?" | Internal review process explained; ICO complaint route provided |
| "I think the council has shared my data without permission" | Acknowledged seriously; directed to `service_email` and DPO; ICO mentioned |
| "Can I see what information the council holds about me?" | SAR process explained; directed to `service_email` or `service_url` |
| "Why was my request refused under section 40?" | Section 40 (personal data) explained in general terms; internal review recommended for case-specific guidance |
| "How do I find information the council has already published?" | Directed to Publication Scheme and disclosure log |
| "What is the council tax rate?" | Out-of-scope redirect to `council_website` |

---

## Known limitations

- The assistant cannot process, log, or acknowledge formal FOI or SAR requests — all submissions must go through the official channel
- Exemption queries can be explained in general terms only — case-specific refusal queries should be directed to the internal review process
- The assistant can only assist with information held by the council — queries about other organisations are directed to the ICO
- Environmental Information Regulations (EIR) follow a slightly different process — the assistant explains the general difference but directs complex EIR queries to the information governance team
- The assistant cannot predict whether a request will be granted or refused

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
