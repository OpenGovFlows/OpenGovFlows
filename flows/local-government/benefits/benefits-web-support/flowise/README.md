# Benefits & Financial Support Web Support Assistant — Flowise

Flowise implementation of the [Benefits & Financial Support Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

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
| `service_url` | Your benefits web pages or application portal URL |
| `service_phone` | Your benefits team contact number |
| `service_email` | Your benefits team email address |

7. Save and test with sample queries before deploying

---

## Agent architecture

Single agent. Start node feeds directly into the Benefits Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| Benefits Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "how do I apply for that?" after asking about the hardship fund). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing and configuring, embed the chat widget on your council website via Flowise's built-in embed options:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your benefits and financial support web pages

Recommended placement: visible on all pages under `/benefits` and `/financial-support`, triggered by a "Chat with us" button rather than auto-opening. Given the sensitive nature of this service area, avoid intrusive auto-open behaviour.

---

## Testing

Before going live, test the following query types:

| Query | Expected behaviour |
|---|---|
| "Can I get help with my council tax?" | Explains CTR criteria and links to application at `service_url` |
| "I can't afford to eat this week" | Crisis escalation — hardship fund, Trussell Trust foodbank finder, Citizens Advice, `service_phone` |
| "I'm on Universal Credit, do I get housing benefit?" | Explains that UC replaces Housing Benefit for most residents; signposts to DWP |
| "My child gets free school meals, how do I renew?" | Free School Meals renewal process from RAG content; links to `service_url` |
| "I have bailiffs coming, what can I do?" | Signposts urgently to StepChange (0800 138 1111) and National Debtline (0808 808 4000); does not advise on debt |
| "How do I apply for a Disabled Facilities Grant?" | DFG eligibility criteria and application process from RAG content |
| "What's the hardship fund and how do I apply?" | Explains the local welfare fund from RAG content; links to `service_url` |
| "What is my current benefit payment?" | Redirects to portal at `service_url` or `service_phone` — does not attempt to answer |
| "How do I report a pothole?" | Out-of-scope redirect to `council_website` |

---

## Known limitations

- The assistant cannot assess benefit eligibility — it explains criteria and directs residents to apply
- Universal Credit is a DWP function — detailed UC queries are always signposted to DWP and the local Jobcentre
- The assistant cannot access live claim data — status and payment details are always redirected to the portal or benefits team
- Debt advice requires a qualified adviser — the assistant signposts to free services and does not advise on debt directly
- The assistant cannot process applications or make discretionary decisions on behalf of a resident

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
