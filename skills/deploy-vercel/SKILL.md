---
name: deploy-vercel
plugin: deploy
description: Deploy a project to Vercel using the Vercel CLI. Handles first-time setup, preview deploys, production deploys, environment variables, custom domains, and build failure troubleshooting.
triggers:
  - "deploy to vercel"
  - "vercel deploy"
  - "push to production"
  - "ship this"
  - "go live"
---

# Deploy to Vercel

## Prerequisites

### 1. Check Vercel CLI
```bash
which vercel
```
If missing: `npm install -g vercel`

### 2. Check Authentication
```bash
vercel whoami
```
If not logged in: `vercel login` → browser flow.

---

## Step 1: First-Time Setup

Check if project is already linked:
```bash
ls -la .vercel/project.json 2>/dev/null
```

If not linked, run `vercel` and walk the user through prompts:

| Prompt | Answer |
|--------|--------|
| Set up and deploy? | **Y** |
| Which scope? | User's account or team |
| Link to existing project? | **N** for new |
| Project name? | Accept default or custom |
| Code directory? | `.` |
| Modify settings? | **N** for most projects |

After linking, confirm: "✅ Project linked — `.vercel/project.json` created."

---

## Step 2: Preview Deploy

```bash
vercel
```

Expected output:
```
✅  Preview: https://<project>-<hash>.vercel.app
```

Report to user: "✅ Preview deploy ready: `<url>`. Review it and say 'deploy to production' when satisfied."

---

## Step 3: Production Deploy

```bash
vercel --prod
```

Expected output:
```
✅  Production: https://<project>.vercel.app
```

Report: "🚀 Live at `<url>`."

---

## Environment Variables

```bash
# Add to production
vercel env add DATABASE_URL production

# List all
vercel env ls

# Pull to local .env.local
vercel env pull .env.local

# Remove
vercel env rm DATABASE_URL production
```

---

## Custom Domains

```bash
vercel domains add myapp.com
```

DNS records Vercel requires:
| Type | Name | Value |
|------|------|-------|
| `A` | `@` | `76.76.21.21` |
| `CNAME` | `www` | `cname.vercel-dns.com` |

---

## Build Failures

```bash
vercel logs <deployment-url>
```

| Error | Cause | Fix |
|-------|-------|-----|
| `Command "npm run build" exited with 1` | Build script error | Check local `npm run build` first |
| `Module not found` | Missing dependency | `npm install <dep>` and redeploy |
| `Out of Memory` | Bundle too large | Add `NODE_OPTIONS=--max_old_space_size=4096` |
| `Environment variable undefined` | Missing env var | `vercel env add <NAME> production` |
| Function timeout | Serverless fn too slow | Optimize or upgrade Vercel plan |

---

## Guardrails

- Never run `vercel --prod` without user confirmation after preview review
- If `.vercel/` is gitignored, remind user to set up project on each new machine
- Warn if sensitive values are in `.env` files that might be committed to git
