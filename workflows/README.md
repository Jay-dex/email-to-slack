# `workflows/`

This folder holds **exported n8n workflow JSON files**.

## Current workflows

| File | Purpose | Status |
|---|---|---|
| `email-to-slack.json` | The full pipeline (Gmail → dedup → Gemini → Slack → mark processed) | ✅ Ready to import |

## How to import a workflow into n8n

1. In the n8n UI: **Workflows → ⋯ → Import from File**
2. Pick the JSON file (e.g. `email-to-slack.json`)
3. n8n will warn about any **credential IDs, Sheet IDs, Slack channel IDs, or API keys** that were saved as placeholders in the JSON — open each node and:
   - Pick the matching credential from the dropdown (Gmail, Google Sheets, Slack)
   - Paste your real **Google Sheet ID** into the `Dedup Lookup` and `Mark Processed` nodes
   - Paste your real **Slack channel ID** into the `Build Slack Message` and `Post to Slack` nodes
   - Paste your real **Gemini API key** into the `Gemini Summarize` node's `x-goog-api-key` header
4. **Activate** the workflow

See the root `README.md` for full setup details.
