# Getting started

This guide walks you through importing and running your first Open Gov Flows workflow. It assumes you have a running Flowise instance. If you do not, see the [Flowise documentation](https://docs.flowiseai.com/getting-started) to get set up first.

---

## Step 1: Choose a flow

Browse the flow catalogue in the repository and choose a flow relevant to your organisation.

Each flow folder contains a `README.md` with what the flow does, what inputs it expects, what outputs it produces, and its governance requirements. Read this before importing — pay particular attention to the governance notes.

---

## Step 2: Download the flow

Navigate to the flow folder on GitHub and download the `flow.json` file. You can do this by clicking `flow.json` in the GitHub file browser and selecting **Download raw file**, or via curl:

```bash
curl -O https://raw.githubusercontent.com/opengovflows/open-gov-flows/main/flows/[sector]/[flow-name]/flow.json
```

---

## Step 3: Import into Flowise

1. Open your Flowise instance
2. Navigate to **Chatflows** or **Agentflows** depending on the type
3. Click **Settings** (top right)
4. Click **Import** and select the `flow.json` file
5. The flow will appear in your list

---

## Step 4: Configure credentials

Each flow's `config.example.json` lists the environment variables and API keys required. In Flowise, add these under **Credentials** and assign them to the relevant nodes in the flow.

Never paste API keys directly into flow nodes — always use Flowise's credential manager.

---

## Step 5: Test before deploying

Before using any flow with real data or in a live environment:

- Run it against the sample inputs in the flow's `README.md`
- Review the outputs critically — does it do what you expected?
- Test edge cases: short inputs, unusual formatting, incomplete data
- Have a subject matter expert review a sample of outputs

No flow should go into production without human review of test outputs. See [docs/governance.md](./governance.md) for the minimum governance steps required before live deployment.

---

## Getting help

- Check the flow's `README.md` — common issues are usually documented there
- Search [GitHub Issues](https://github.com/opengovflows/opengovflows/issues)
- Open a new issue if your question is not already answered
- For bugs, also [open an issue](https://github.com/opengovflows/open-gov-flows/issues)
