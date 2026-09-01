# Deployment Guide

> Filled in when we're ready to move off the laptop. For now, we run n8n via `npx n8n`.

## Recommended target: Oracle Cloud Free Tier

- **Always-free** ARM VM (4 OCPU, 24 GB RAM is more than enough)
- Static public IP
- Survives reboots
- No credit card charges if you stick to the free tier

### Rough steps (when the time comes)
1. Provision a free-tier VM (Ubuntu 22.04+)
2. Open port 80/443 (and 5678 for testing) in the security list
3. SSH in, install Docker + Docker Compose
4. `git clone` this repo (or `scp` it up)
5. Copy `.env.example` → `.env` and fill in real values
6. `docker compose up -d`
7. Put **Caddy** in front for automatic HTTPS
8. Point a domain (optional) at the VM's IP

## Alternative targets

- **Render** — easiest "git push to deploy" experience
- **Railway** — similar to Render, generous free tier
- **fly.io** — global edge, generous free tier

## Things to remember when deploying

- n8n's web UI must be behind auth (use `N8N_BASIC_AUTH_*` env vars, or front it with Caddy basic auth)
- Set `N8N_HOST` to the public URL so OAuth callbacks work
- Re-authorize Gmail + Slack OAuth after changing the public URL
- The dedup Google Sheet keeps working — same OAuth account works everywhere
