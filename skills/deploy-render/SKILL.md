---
name: deploy-render
plugin: deploy
description: Deploy to Render using render.yaml (Infrastructure as Code) or the Render dashboard. Handles web services, background workers, cron jobs, and static sites.
triggers:
  - "deploy to render"
  - "render deploy"
  - "push to render"
---

# Deploy to Render

## Overview

Render deploys via **Git push** — connect your repo once, every push to your branch triggers a deploy. Render has no dedicated CLI (as of 2025); deployments are configured via `render.yaml` (Blueprint) and triggered via git.

---

## Step 1: Create render.yaml

If `render.yaml` doesn't exist, create it:

**Static Site (Vite, CRA, Astro):**
```yaml
services:
  - type: web
    name: my-app
    runtime: static
    buildCommand: npm run build
    staticPublishPath: dist
    pullRequestPreviewsEnabled: true
    routes:
      - type: rewrite
        source: /*
        destination: /index.html
```

**Node.js Web Service:**
```yaml
services:
  - type: web
    name: my-api
    runtime: node
    buildCommand: npm install && npm run build
    startCommand: npm start
    envVars:
      - key: NODE_ENV
        value: production
      - key: DATABASE_URL
        sync: false   # set manually in Render dashboard
```

**Python/FastAPI:**
```yaml
services:
  - type: web
    name: my-api
    runtime: python
    buildCommand: pip install -r requirements.txt
    startCommand: uvicorn main:app --host 0.0.0.0 --port $PORT
```

**Background Worker:**
```yaml
services:
  - type: worker
    name: my-worker
    runtime: node
    buildCommand: npm install && npm run build
    startCommand: node dist/worker.js
```

**Cron Job:**
```yaml
services:
  - type: cron
    name: nightly-job
    runtime: node
    buildCommand: npm install
    startCommand: node scripts/nightly.js
    schedule: "0 2 * * *"   # 2am UTC daily
```

---

## Step 2: Connect to Render

1. Push `render.yaml` to your git repo
2. Go to **dashboard.render.com** → New → Blueprint
3. Connect your GitHub/GitLab repo
4. Render reads `render.yaml` and creates the service(s)

Or connect individual service: New → Web Service → select repo → Render auto-detects framework.

---

## Step 3: Trigger Deploy

Every `git push` to the configured branch triggers a deploy automatically.

To trigger a manual deploy: Render Dashboard → Service → Manual Deploy → Deploy latest commit.

---

## Environment Variables

Set sensitive values in **Render Dashboard** → Service → Environment:
- Click "Add Environment Variable"
- Mark as "Secret" to prevent display in logs

Or define non-secret values in `render.yaml`:
```yaml
envVars:
  - key: NODE_ENV
    value: production
```

For secrets always use `sync: false` in render.yaml and set manually in dashboard:
```yaml
envVars:
  - key: DATABASE_URL
    sync: false
```

---

## Custom Domains

Render Dashboard → Service → Custom Domains → Add Domain.

DNS: Add `CNAME` record pointing to `<service>.onrender.com`.

---

## Common Issues

| Issue | Cause | Fix |
|-------|-------|-----|
| Service spins down | Free tier suspends after 15 min inactivity | Upgrade to paid or use external pinger |
| PORT not bound | App not using `process.env.PORT` | Render assigns a dynamic port — always use `process.env.PORT` |
| Build fails | Wrong build command | Check Render deploy logs; test `npm run build` locally |
| Static routes 404 | SPA routing not configured | Add rewrite rule `/* → /index.html` in render.yaml |

---

## Guardrails

- Render free tier **sleeps after 15 minutes** — warn user if they expect always-on
- Always set `process.env.PORT` — Render assigns the port dynamically
- `render.yaml` is the recommended way to manage services as code
