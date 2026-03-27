# Open Gov Flows

**The open standard for public sector AI workflows.**

A free, open-source library of production-ready AI workflows built specifically for UK public sector organisations. Built on [Flowise](https://flowiseai.com). Free to use. Free to adapt. Community maintained.

---

## What is this?

Open Gov Flows is a library of pre-built AI workflows covering the most common use cases in local government, NHS, central government, and ALB's. Each flow is a tested, documented Flowise export you can import into your own Flowise instance and run immediately.

No vendor lock-in. No licensing fees. No proprietary dependencies.

---

## Who is it for?

- Digital teams in local authorities, NHS trusts, and central government
- Transformation leads exploring AI adoption
- Developers and suppliers building on behalf of public sector clients
- Anyone who wants to see what practical public sector AI looks like

---

## Getting started in 5 minutes

**Prerequisites:** A running Flowise instance (v2.x+). [How to install Flowise →](https://docs.flowiseai.com/getting-started)

1. Browse the flows above and find one relevant to your organisation
2. Download the `flow.json` file from the flow's folder
3. In your Flowise instance: **Chatflows/Agentflows -> Add New -> Settings -> Load Chatflow/Agents**  → select the JSON file
4. Configure the required API keys and settings (see each flow's `README.md`)
5. Test with sample data and do due diligence before deploying to real users

**Need help?** [Open an issue →](https://github.com/opengovflows/opengovflows/issues)

---

## Using a flow

Each flow folder contains:

```
flow-name/
├── flow.json           # The Flowise export — import this
├── manifest.json       # Metadata: sector, use case, requirements
├── README.md           # What it does, how to configure it, example outputs
├── config.example.json # Environment variables you'll need to set
└── CHANGELOG.md        # Version history
```

See [docs/using-a-flow.md](./docs/using-a-flow.md) for full guidance.

---

## Contributing a flow

Contributions are welcome and encouraged. If you've built a flow that solves a public sector problem, share it here.

See [CONTRIBUTING.md](./CONTRIBUTING.md) for how to submit a flow, documentation standards, and the review process.

We ask that all contributions:
- Solve a real, named public sector use case
- Include a complete `README.md` and `manifest.json`
- Have been tested in a real or realistic environment
- Do not include any real personal data or sensitive information

---

## Governance and safety

These flows are provided for evaluation and adaptation. Before deploying any AI workflow in a live public sector environment you should:

- Complete an AI risk assessment (see our [AI Risk Assessment Generator flow](./flows/cross-sector/ai-risk-assessment-generator/))
- Review outputs against your organisation's information governance policy
- Ensure compliance with UK GDPR and any sector-specific regulations
- Obtain appropriate sign-off from your DPO and senior responsible owner

See [docs/governance.md](./docs/governance.md) for guidance.

---

## Roadmap

[View full roadmap →](https://github.com/opengovflows/open-gov-flows/issues)

---

## Maintained by

Open Gov Flows is maintained by [Inovtra](https://invotra.com) and the open source community.

Invotra builds the AI governance and workflow platform for UK public sector. Open Gov Flows is the open layer underneath it — free forever, community owned.

[Learn about Invotra →](https://invotra.com)

---

## Licence

MIT. Use it, adapt it, deploy it. See [LICENSE](./LICENSE).
