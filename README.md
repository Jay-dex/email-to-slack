# Email Intelligence to Slack

An automated email-intelligence workflow built with **n8n** that monitors a Gmail inbox,
summarizes incoming emails with **Google Gemini**, and posts concise, structured summaries
to a dedicated **Slack** channel — so important information surfaces in one place
instead of getting buried in the inbox.

> See [`docs/project_context.md`](docs/project_context.md) for the full project brief.

---

## ✨ What it does

- Watches a Gmail inbox for new messages (push-based trigger)
- Skips emails that have already been processed (idempotent via a Google Sheet log)
- Sends each new email to **Gemini** with a structured prompt
- Receives back a JSON summary: `summary`, `priority`, `action_items[]`, `deadline`
- Posts a **rich Slack Block Kit** message to your channel with priority tagged
- Logs every processed `messageId` so nothing is ever posted twice
- Routes failures to a separate sheet/channel instead of silently dropping them

---

## 🧱 Architecture

```
Gmail (push) → n8n workflow → dedup check (Google Sheet)
                                 ↓ (new only)
                            Gemini summarization (JSON)
                                 ↓
                            Build Slack Block Kit message
                                 ↓
                            Post to Slack channel
                                 ↓
                            Mark processed in Google Sheet
```

---

## 🚀 Quick start (local)

### 1. Prerequisites
- Node.js 18+ (for running n8n via `npx`)
- A Google account (for Gmail + the dedup Sheet)
- A Slack workspace with permission to add an app
- A Google AI Studio API key (for Gemini — free tier is fine)

### 2. Install and run n8n
```bash
npx n8n
```
n8n will open at <http://localhost:5678>. The first time, it will ask you to create
a local owner account.

### 3. Set up credentials
In the n8n UI, add credentials for:
- **Gmail** (OAuth2 — read-only scope)
- **Slack** (OAuth2 — `chat:write` scope)
- **Google Gemini (PaLM) API** (HTTP header auth with your API key)
- **Google Sheets** (OAuth2 — same Google account)

### 4. Prepare the Google Sheet dedup log
Create a Google Sheet named `email-to-slack-log` with a sheet tab `processed` and
columns: `messageId | subject | sender | processedAt`.

### 5. Import the workflow
1. In n8n: **Workflows → Import from File**
2. Select `workflows/email-to-slack.json`
3. Open each credential-using node and pick your saved credentials
4. Set the Slack channel ID
5. **Activate** the workflow

### 6. Test
Send yourself a test email and verify:
- A new row appears in the Sheet
- A formatted message appears in Slack within seconds
- A re-send of the same email does NOT create a duplicate

---

## ⚙️ Environment variables

Copy `.env.example` → `.env` and fill in. **Never commit `.env`.**

| Var | Purpose |
|---|---|
| `N8N_HOST` | Public URL when deploying (leave blank locally) |
| `N8N_PORT` | Defaults to `5678` |
| `N8N_BASIC_AUTH_ACTIVE` | Enable basic auth for the n8n web UI |
| `N8N_BASIC_AUTH_USER` | n8n login username |
| `N8N_BASIC_AUTH_PASSWORD` | n8n login password |
| `GEMINI_API_KEY` | Google AI Studio API key |
| `SLACK_CHANNEL_ID` | Target channel (e.g. `C0123ABCD`) |
| `GOOGLE_SHEET_ID` | The Sheet used for the dedup log |

---

## 📦 Deployment (later)

A `docker-compose.yml` is included so the same workflow can run on any VPS
(Oracle Cloud free tier, Render, Railway, fly.io, etc.). See `docs/deploy.md`
when you're ready to take it off your laptop.

---

## 📁 Project layout

```
email-to-slack/
├── README.md
├── .env.example          # template — copy to .env
├── .gitignore
├── docker-compose.yml    # for deployment (not used locally)
├── package.json          # n8n + helpful scripts
├── workflows/
│   └── email-to-slack.json   # the n8n workflow export
└── docs/
    ├── project_context.md
    └── deploy.md
```

---

## 🛣️ Future scope (from the brief)

- Priority-based filtering (only post Medium/High to Slack)
- Auto-extract tasks and deadlines → push to a task manager
- Create calendar events from detected deadlines
- Draft reply suggestions for approval
- Connect more services (Notion, Trello, etc.)
