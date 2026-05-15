# Housing & Homelessness Web Support Assistant — Flowise

Flowise implementation of the [Housing & Homelessness Web Support Assistant](../README.md). Built as a single-agent agentflow on Flowise 3.x.

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
| `service_url` | Your housing web pages or housing portal URL |
| `service_phone` | Your housing duty contact number (must include out-of-hours emergency line) |
| `service_email` | Your housing team email address |

7. Save and test with sample queries before deploying

> **Before going live:** confirm the `service_phone` number is staffed out of hours. Residents presenting as homeless at night will be directed to this number.

---

## Agent architecture

Single agent. Start node feeds directly into the Housing & Homelessness Support Agent.

| Node | Model | Temperature | Memory |
|---|---|---|---|
| Housing & Homelessness Support Agent | gpt-4o-mini | 0.1 | All Messages |

Memory is enabled so the agent can handle follow-up questions within a session (e.g. "how long does that take?" after asking about the housing register). Session memory clears when the conversation ends.

---

## Embedding the widget

After importing and configuring, embed the chat widget on your council website via Flowise's built-in embed options:

1. In Flowise, open the agentflow
2. Click the **\</>** embed icon (top right)
3. Choose **Popup** or **Full Page** depending on your website layout
4. Copy the embed script and add it to your housing web pages

Recommended placement: visible on all pages under `/housing` and `/homelessness`, triggered by a "Chat with us" button rather than auto-opening. Given the sensitive nature of this service area, avoid intrusive auto-open behaviour.

---

## Testing

Before going live, test the following query types:

| Query | Expected behaviour |
|---|---|
| "How do I apply for council housing?" | Explains housing register process and links to `service_url` |
| "I'm homeless and have nowhere to go tonight" | Immediate escalation — provides `service_phone`, explains legal duty, does not attempt to assess |
| "My boiler has broken, I'm a council tenant" | Emergency repair guidance; links to repair reporting form or `service_phone` |
| "I want to buy my council house" | Right to Buy eligibility and process from RAG content |
| "My landlord is evicting me" | Homelessness prevention advice; mentions Shelter helpline; provides `service_phone` |
| "There's someone sleeping rough near me" | StreetLink referral and `service_phone` for rough sleeping team |
| "I'm fleeing domestic abuse and need housing" | Provides `service_phone` and Refuge helpline (0808 2000 247); does not ask for details |
| "How long is the housing waiting list?" | General explanation of how CBL works; directs to `service_url` for local position |
| "What's the council tax rate?" | Out-of-scope redirect to `council_website` |

---

## Known limitations

- The assistant cannot access live housing application data — register position, bidding history, repair status, and tenancy details are always redirected to the housing portal or the housing team
- Homelessness duty assessments require a qualified officer — the assistant provides information and signposting only
- Domestic abuse disclosures are escalated immediately — the assistant is not equipped to handle these beyond providing emergency contact details
- Complex allocations eligibility queries should always be referred to a housing officer, even when the policy is loaded into the RAG store
- The assistant cannot submit repair reports, housing applications, or Right to Buy applications on behalf of a resident

---

## Cost estimate

| Model | Typical query | Estimated cost per conversation |
|---|---|---|
| gpt-4o-mini | ~200 tokens in, ~150 tokens out | ~$0.0001 |

At scale (10,000 conversations/month), estimated cost: ~$1–2/month. Costs correct as of May 2026. Check [OpenAI pricing](https://openai.com/pricing) for current rates.
