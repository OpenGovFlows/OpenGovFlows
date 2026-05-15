# Parking & Roads Web Support Assistant — Flowise

Flowise implementation of the [Parking & Roads Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

> **Note:** The agent node in this flow is labelled "Parking & Travel Support Assistant" in Flowise. This reflects the broader scope of travel and highways queries the flow handles.

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
| `service_url` | Your parking and roads web pages or PCN payment portal URL |
| `service_phone` | Your parking and highways contact number |
| `service_email` | Your parking appeals and highways contact email |

7. Save and test with sample queries before deploying

---

## Agent architecture

Single agent. Start node feeds directly into the Parking & Travel Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| Parking & Travel Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "what happens if that is rejected?" after asking about the informal PCN challenge). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing and configuring, embed the chat widget on your council website via Flowise's built-in embed options:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your parking and roads web pages

Recommended placement: visible on all pages under `/parking` and `/roads`, triggered by a "Chat with us" button rather than auto-opening. Consider also placing on the PCN payment page — residents who land there often have questions before paying.

---

## Testing

Before going live, test the following query types:

| Query | Expected behaviour |
|---|---|
| "How do I pay my parking ticket?" | Directs to PCN payment portal at `service_url` with PCN reference guidance |
| "I want to appeal my parking fine" | Explains three-stage process; links to informal challenge form at `service_url` |
| "There's a massive pothole on my road" | Reporting guidance; link to FixMyStreet or council form; emergency escalation if dangerous |
| "My street light has been out for a week" | Reporting guidance; link to streetlighting fault report form |
| "How do I get a residents parking permit?" | Eligibility and application process from RAG content; links to `service_url` |
| "Can I park on a single yellow line on a Sunday?" | Explains yellow line rules and controlled hours; advises checking signs on site |
| "How do I apply for a Blue Badge?" | Directs to gov.uk/apply-blue-badge; does not attempt to assess eligibility |
| "The road outside my house is terrible, who is responsible?" | Explains highway adoption; directs to `service_phone` to confirm if adopted |
| "What is the council tax rate?" | Out-of-scope redirect to `council_website` |

---

## Known limitations

- The assistant cannot process PCN payments — residents are always directed to the payment portal
- The assistant cannot advise on whether a PCN appeal will succeed — that is a decision for the parking adjudicator
- Without a live reporting integration, the assistant cannot confirm receipt of a pothole or fault report
- Blue Badge eligibility involves medical criteria — the assistant directs to GOV.UK and does not assess eligibility
- Highway adoption status for specific roads requires data in the RAG store or a call to the highways team

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
