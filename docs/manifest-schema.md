# Manifest schema

Every flow in Open Gov Flows includes a `manifest.json` file. This is the canonical reference for every field.

Copy the template at the bottom of this page into your flow folder and fill in all fields before submitting a PR.

---

## Full schema

```json
{
  "id": "",
  "name": "",
  "version": "",
  "status": "",
  "description": "",

  "sector": [],
  "use_case_category": "",
  "tags": [],

  "flowise": {
    "min_version": "",
    "tested_version": "",
    "flow_file": "flow.json"
  },

  "requirements": {
    "llm": {
      "provider": "",
      "model": "",
      "notes": ""
    },
    "environment_variables": [
      {
        "key": "",
        "required": true,
        "description": ""
      }
    ]
  },

  "inputs": [
    {
      "name": "",
      "type": "",
      "description": "",
      "required": true
    }
  ],

  "outputs": [
    {
      "name": "",
      "type": "",
      "description": ""
    }
  ],

  "governance": {
    "human_review_required": true,
    "human_review_notes": "",
    "data_sensitivity": "",
    "data_notes": "",
    "risk_level": "",
    "iso_42001_aligned": true
  },

  "licence": "MIT"
}
```

---

## Field reference

### Top level

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Unique identifier. Must match the folder name exactly. Kebab-case. Example: `foi-request-triage` |
| `name` | string | yes | Human-readable name. Example: `FOI Request Triage` |
| `version` | string | yes | Semantic version. Start at `1.0.0`. Increment on any change to the flow or its behaviour. |
| `status` | string | yes | `stable` or `experimental` — see [status values](#status-values) below |
| `description` | string | yes | Two to three sentences. What it does, what it takes in, what it produces. |

### Classification

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `sector` | array | yes | One or more sector strings. Examples: `local-gov`, `nhs`, `central-gov`, `policing`, `cross-sector`. Use what fits — this list is not exhaustive. |
| `use_case_category` | string | yes | A short string describing the category of use case. Examples: `citizen-requests`, `casework`, `clinical-admin`, `governance`, `correspondence`. Use what fits — this list is not exhaustive. |
| `tags` | array | yes | Freeform lowercase tags for search. Include the use case, relevant legislation, and relevant role. Example: `["foi", "freedom-of-information", "triage", "legal"]` |

### `flowise`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `min_version` | string | yes | Minimum Flowise version required. Example: `2.0.0` |
| `tested_version` | string | yes | The exact Flowise version you tested against. Example: `2.1.4`. Set to `null` if not yet tested. |
| `flow_file` | string | yes | Always `flow.json` |

### `requirements`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `llm.provider` | string | yes | LLM provider. Example: `openai`, `azure-openai`, `anthropic` |
| `llm.model` | string | yes | Recommended model. Example: `gpt-4o` |
| `llm.notes` | string | no | Substitution notes. Example: `GPT-4o-mini works for simpler requests.` |
| `environment_variables` | array | yes | All environment variables needed to run the flow. Include key, whether required, and a plain-English description. |

### `inputs` and `outputs`

List every significant input the flow accepts and every output it produces. Use plain English for `description` — this is what a non-technical officer will read.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | yes | Short identifier |
| `type` | string | yes | `text`, `document`, `json`, `url` |
| `description` | string | yes | What this input or output is in plain English |
| `required` | boolean | inputs only | Whether this input is mandatory |

### `governance`

Reviewed most carefully. Do not understate risk or data sensitivity.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `human_review_required` | boolean | yes | Must be `true` for any flow whose outputs could affect citizens, involve legal considerations, or inform decisions. |
| `human_review_notes` | string | yes | Specific guidance on what a reviewer should check. Be specific to this flow — not a generic statement. |
| `data_sensitivity` | string | yes | `low`, `medium`, or `high` — see [data sensitivity values](#data-sensitivity-values) below |
| `data_notes` | string | yes | What personal or sensitive data this flow may process and any specific handling requirements. |
| `risk_level` | string | yes | `low`, `medium`, or `high` — see [risk level values](#risk-level-values) below |
| `iso_42001_aligned` | boolean | yes | `true` only if all governance fields are complete and accurate. |

---

## Allowed values

### Status values

| Value | Meaning |
|-------|---------|
| `stable` | Tested in a real or realistic environment. Outputs have been reviewed by a subject matter expert. |
| `experimental` | Builds on a real use case but not yet tested in a live environment. Use with additional caution. |

### Risk level values

| Value | Meaning |
|-------|---------|
| `low` | Assistive only. No direct citizen impact. Outputs are easily verified by a non-expert. |
| `medium` | Assists with decisions or correspondence that affects citizens or has reputational implications. |
| `high` | Assists with decisions with significant legal, clinical, financial, or safeguarding consequences. Requires additional governance steps before deployment. |

### Data sensitivity values

| Value | Meaning |
|-------|---------|
| `low` | No personal data. Internal documents only. |
| `medium` | May include personal data such as names, addresses, or case references. Standard UK GDPR controls apply. |
| `high` | Likely to include special category data (health, criminal justice, etc.) or bulk personal data. Enhanced controls and DPIA likely required. |

---

## Completed example

The [FOI Request Triage](../flows/local-gov/foi-request-triage/manifest.json) flow provides a complete worked example of a correctly completed manifest.

---

## Template

Copy this into your flow folder as `manifest.json` and fill in every field:

```json
{
  "id": "your-flow-name",
  "name": "Your Flow Name",
  "version": "1.0.0",
  "status": "experimental",
  "description": "Two to three sentences. What it does, what goes in, what comes out.",

  "sector": ["local-gov"],
  "use_case_category": "citizen-requests",
  "tags": ["tag-one", "tag-two"],

  "flowise": {
    "min_version": "2.0.0",
    "tested_version": null,
    "flow_file": "flow.json"
  },

  "requirements": {
    "llm": {
      "provider": "openai",
      "model": "gpt-4o",
      "notes": ""
    },
    "environment_variables": [
      {
        "key": "OPENAI_API_KEY",
        "required": true,
        "description": "Your OpenAI API key"
      }
    ]
  },

  "inputs": [
    {
      "name": "input_name",
      "type": "text",
      "description": "Plain English description of what this input is.",
      "required": true
    }
  ],

  "outputs": [
    {
      "name": "output_name",
      "type": "text",
      "description": "Plain English description of what this output contains."
    }
  ],

  "governance": {
    "human_review_required": true,
    "human_review_notes": "Specific guidance on what a reviewer must check before acting on this output.",
    "data_sensitivity": "medium",
    "data_notes": "Describe what personal or sensitive data this flow may process.",
    "risk_level": "medium",
    "iso_42001_aligned": true
  },

  "licence": "MIT"
}
```
