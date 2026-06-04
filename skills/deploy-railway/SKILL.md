---
name: deploy-railway
plugin: deploy
description: Deploy to Railway using the Railway CLI. Handles project setup, deployments, environment variables, and service configuration.
triggers:
  - "deploy to railway"
  - "railway deploy"
  - "push to railway"
---

# Deploy to Railway

## Prerequisites

```bash
which railway
# If missing: npm install -g @railway/cli
railway whoami
# If not logged in: railway login → browser OAuth
```

---

## Step 1: Link or Initialize Project

```bash
ls railway.json 2>/dev/null || ls railway.toml 2>/dev/null
```

If no config found:
```bash
railway init
```

Prompts:
| Prompt | Answer |
|--------|--------|
| Create new project? | **Y** |
| Project name | Accept or set custom |
| Add initial service? | **Y** — select your service type |

If project exists on Railway already:
```bash
railway link
```
Select team → project → environment.

---

## Step 2: Deploy

```bash
railway up
```

Expected output:
```
Deployment started...
✅ https://<project>.up.railway.app
```

For a specific service in a monorepo:
```bash
railway up --service <service-name>
```

---

## Step 3: Open Running Service

```bash
railway open
```

Opens the Railway dashboard for the project.

---

## Environment Variables

```bash
# Set a variable
railway variables set DATABASE_URL=<value>

# List all variables
railway variables

# Set from .env file
railway variables set --from .env
```

---

## Logs

```bash
railway logs
railway logs --tail
```

---

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `No linked project found` | Not linked to Railway project | `railway link` |
| `Build failed` | Build script error | Run `npm run build` locally first |
| `PORT must be set` | App not reading `process.env.PORT` | Add `const PORT = process.env.PORT \|\| 3000` |
| `nixpacks error` | Framework not auto-detected | Add `railway.toml` with explicit build config |

---

## railway.toml (if needed)

```toml
[build]
builder = "nixpacks"
buildCommand = "npm run build"

[deploy]
startCommand = "npm start"
healthcheckPath = "/health"
restartPolicyType = "on_failure"
```

---

## Guardrails

- Railway auto-detects most Node.js, Python, Go, Ruby, and Rust projects
- Always ensure `process.env.PORT` is used — Railway assigns a dynamic port
- Never commit sensitive env vars; use `railway variables set` instead
