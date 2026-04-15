# Contributing to Open Gov Flows

Thank you for contributing. Every flow added here saves time for public sector teams across the UK.

---

## What we accept

We accept flows that:

- Solve a **real, named public sector use case** — not a generic AI demo
- Have been **tested** in a real or realistic Flowise environment
- Include **complete documentation** following the standards below
- Contain **no real personal data, credentials, or internal system references**
- Are built on **Flowise** — we do not currently accept flows for other platforms

We are particularly interested in flows covering:

- Local government (planning, housing, revenues & benefits, legal, democratic services)
- NHS and health (clinical admin, patient communications, information governance)
- Policing and justice
- Central government (policy, correspondence, procurement, FOI)
- Cross-sector governance and compliance
- Arms length bodies

If you are unsure whether your flow is a good fit, [open an issue](https://github.com/opengovflows/opengovflows/issues) before building.

---

## How to contribute a flow

### 1. Check for duplicates

Browse existing flows and [open issues](https://github.com/opengovflows/opengovflows/issues) before starting. If a similar flow exists, consider improving it rather than duplicating it.

### 2. Fork the repo and create your flow folder

Flows live under `flows/[sector]/[flow-name]/`. Use kebab-case for folder names.

Example: `local-gov` · `nhs` · `central-gov` · `policing` · `cross-sector`

Your flow folder must contain:

```
flows/
└── [sector]/
    └── [your-flow-name]/
        ├── README.md                ← what the flow does, links to platforms
        └── [platform]/              ← e.g. flowise/, n8n/, copilot-studio/
            ├── workflow.json        ← platform export
            ├── manifest.json        ← metadata
            └── README.md            ← platform-specific setup instructions
```

### 3. Complete the manifest

Copy the manifest from any existing flow as a template or use our provided one under /docs. Every field is required. The fields reviewers look at most closely:

- 'platform' - must match your subfolder name exactly (e.g. flowise, n8n, copliot-studio)
- `status` — use `stable` or `experimental` honestly
- `governance.human_review_required` — must be `true` for anything affecting citizens
- `governance.risk_level` — do not understate this
- `requirements.environment_variables` — list everything needed to run the flow

See the full manifest schema in [docs/manifest-schema.md](./docs/manifest-schema.md).

### 4. Write the README

Every flow README must include these sections, in this order:

- **What it does** — plain English, no jargon
- **Requirements** — Flowise version, LLM, API keys
- **Setup** — step by step from import to first run
- **Example input and output** — with all real data removed
- **Governance notes** — human review requirements, data sensitivity, relevant regulations
- **Known limitations** — be honest

Use our examples as a reference for tone and structure.

### 5. Scrub your workflow.json

Before submitting, verify your `workflowflow.json` contains no:

- API keys, tokens, or credentials of any kind
- Real personal data (names, addresses, NHS numbers, case references, etc.)
- Internal URLs, hostnames, or IP addresses
- Proprietary prompts you do not have the right to share

Use placeholder values in the format `YOUR_VALUE_HERE`.

### 6. Open a pull request

Use the PR title format: `Add flow: [Flow Name] ([Sector])`

Complete the PR template in full. Incomplete PRs will be returned without review.

---

## Improving existing flows

PRs that improve existing flows are very welcome. This includes better prompts, updated compatibility, improved documentation, and additional test cases.

Update `CHANGELOG.md` and bump the version in `manifest.json` with any substantive change.

---

## Documentation-only contributions

Corrections to documentation, improved explanations, and better examples are always welcome. No issue needed — open a PR directly.

---

## Review process

All submitted flows are reviewed by a maintainer for:

- Documentation completeness
- Governance honesty (risk levels, human review requirements)
- Security (no credentials, no real data)
- Flowise compatibility
- Practical usefulness for public sector organisations

We aim to review PRs within **10 working days**. We will suggest changes before merging where needed. We reserve the right to decline flows that do not meet the standards above.

---

## Reporting issues

Found a flow that produces poor outputs, has a security concern, or contains incorrect governance guidance?

[Open an issue](https://github.com/opengovflows/opengovflows/issues).

For any security issues, [report a vulnerability](https://github.com/OpenGovFlows/OpenGovFlows/security).
