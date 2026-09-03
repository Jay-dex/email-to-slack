# `workflows/`

This folder holds **exported n8n workflow JSON files**.

## Current workflows

| File | Purpose | Status |
|---|---|---|
| `email-to-slack.json` | The main pipeline (Gmail → clean body → skip noise → dedup → Gemini → Slack → mark processed) | ✅ Ready to import |
| `email-to-slack-error-handler.json` | Catches failures from the main workflow, posts to Slack + logs to a `failures` Sheet tab | ✅ Ready to import — **activate BEFORE the main workflow** |

## How to import a workflow into n8n

1. In the n8n UI: **Workflows → ⋯ → Import from File**
2. Pick the JSON file (e.g. `email-to-slack.json`)
3. n8n will warn about any **credential IDs, Sheet IDs, Slack channel IDs, or API keys** that were saved as placeholders in the JSON — open each node and:
   - Pick the matching credential from the dropdown (Gmail, Google Sheets, Slack)
   - Paste your real **Google Sheet ID** into the `Dedup Lookup` and `Mark Processed` nodes
   - Paste your real **Slack channel ID** into the `Build Slack Message` and `Post to Slack` nodes
   - Paste your real **Gemini API key** into the `Gemini Summarize` node's `x-goog-api-key` header
4. **Activate** the workflow

> **For the error handler:** create a second tab in your Google Sheet named `failures` with header row `executionId | workflowName | nodeName | nodeType | errorMessage | failedAt`. The error workflow writes to this tab.

## Activation order matters

1. **First:** import + activate `email-to-slack-error-handler.json` — so failures from the main workflow have somewhere to go
2. **Then:** import + activate `email-to-slack.json`

## Recommended: Gmail "Intel" label (optional triage)

To control which emails get processed:

1. In Gmail, create a label called `Intel` (Settings → Labels → Create new label)
2. In n8n's **Gmail Trigger** node, change the filter query from `is:unread in:inbox` to `is:unread in:inbox label:Intel`
3. Now only emails you manually tag with `Intel` will be summarized and posted to Slack

This is optional — leaving the filter as `is:unread in:inbox` processes every new email (the noise filter still drops automated senders like `noreply`).
