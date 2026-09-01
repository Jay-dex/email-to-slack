# `workflows/`

This folder holds **exported n8n workflow JSON files**.

## Current workflows

| File | Purpose | Status |
|---|---|---|
| _(none yet)_ | The email-to-slack pipeline | To be built in Phase 3 |

## How to export a workflow from n8n

1. Open the workflow in the n8n UI
2. Click the **⋯ (three dots)** menu in the top-right
3. Choose **Download**
4. Save the JSON file into this folder with a descriptive name, e.g. `email-to-slack.json`
5. Commit the change

## How to import a workflow into n8n

1. In the n8n UI: **Workflows → ⋯ → Import from File**
2. Pick the JSON file
3. Re-attach credentials to each credential-using node
4. Activate
