# Email Intelligence to Slack — v2.1 (Merged Design)

> **Project:** Automated email-intelligence workflow that monitors a Gmail inbox, cleans and filters each message, summarizes it with **Google Gemini**, and posts a structured, priority-tagged **Slack** Block Kit message to a dedicated channel.
> **Repo:** <https://github.com/Jay-dex/email-to-slack>
> **Status:** v2.1 ready for import into n8n (local dev) — deployment phase pending
> **Last updated:** September 2026

This is the canonical design document. The Desktop file `email-to-slack-v2.md` is a mirror.

---

## 1. Overview

A single n8n workflow watches a Gmail inbox and, for every new email, performs:

1. **Filter** out automated/noise senders (`noreply`, `no-reply`, `donotreply`, `mailer-daemon`)
2. **Clean** the email body (strip HTML, drop quoted reply chains, trim length)
3. **Dedup** against a Google Sheet log
4. **Summarize** with Google Gemini 2.5 Flash
5. **Build** a rich Slack Block Kit message
6. **Post** to a designated Slack channel
7. **Mark** the email as processed in the Sheet

A separate **Error Handler** workflow catches any failure, posts a formatted error to Slack, and logs it to a `failures` Sheet tab.

See the full design doc on the Desktop (`email-to-slack-v2.md`) for node-by-node specs, credentials setup, deployment, and the appendix of fixes between v2 and v2.1.

The 8 fixes that produced v2.1:

| # | What changed | Why |
|---|---|---|
| 1 | Gemini API key now read from `$env.GEMINI_API_KEY` at runtime | Key never gets baked into the workflow JSON on export |
| 2 | `errorTrigger` documentation corrected | It is not a webhook — linked via main workflow Settings → Error Workflow |
| 3 | `N8N_PORT` standardised to 5679 everywhere | Match the actually-running n8n instance |
| 4 | Removed `N8N_BASIC_AUTH_*` env vars | Deprecated and removed in n8n v1.x; replaced with `N8N_ENCRYPTION_KEY` |
| 5 | Added Google's 7-day OAuth refresh token expiry note | Most predictable recurring failure for unverified apps |
| 6 | Added §9.6 documenting the dedup race condition | Real edge case under burst load |
| 7 | Replaced absolute Windows path in §12 | Avoided exposing username in a public-facing doc |
| 8 | Swapped node order: Skip Noise → Clean Body | Filter first, clean second; cheaper and conceptually cleaner |
