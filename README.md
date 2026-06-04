# deploy

A Claude Code plugin for deploying your project to Vercel, Railway, Fly.io, or Render — without leaving your editor.

Auto-detects your framework and deployment platform, runs preflight checks, handles preview and production deployments, and rolls back safely.

## Install

```
/plugin install deploy@mturac
```

## Commands

| Command | Description |
|---------|-------------|
| `/deploy` | Auto-detect platform and deploy preview |
| `/deploy-preview` | Deploy a preview/staging version |
| `/deploy-production` | Deploy to production (requires confirmation) |
| `/deploy-rollback` | Roll back to the previous deployment |
| `/deploy-status` | Check recent deployment status |

## Supported Platforms

| Platform | Config file detected | CLI required |
|----------|---------------------|--------------|
| **Vercel** | `vercel.json`, `.vercel/` | `vercel` |
| **Railway** | `railway.json`, `railway.toml` | `railway` |
| **Fly.io** | `fly.toml`, `.fly/` | `flyctl` |
| **Render** | `render.yaml`, `render.yml` | None (git-based) |

## Framework Detection

Automatically detects: Next.js, Nuxt, SvelteKit, Remix, Astro, Vite, Create React App, Express, Fastify, Hono, Python/FastAPI, Go, and static HTML.

## How it works

1. `/deploy` runs platform detection — identifies your framework, build command, output directory, and target platform
2. Checks CLI is installed and authenticated
3. Runs a **preview deploy** first
4. You review the preview URL, then confirm production deploy
5. Rollback checks for DB migrations before proceeding — warns if migrations are present

## Environment Variables

The plugin guides you through setting env vars on each platform:
- Vercel: `vercel env add NAME production`
- Railway: `railway variables set NAME=value`
- Fly.io: `fly secrets set NAME=value`

## Example

```
You: /deploy

Claude: 🔍 Detected: Next.js + Vercel (vercel.json found)
        Build: npm run build → .next
        CLI: vercel ✅ authenticated

        Running preview deploy...
        ✅ Preview: https://my-app-abc123.vercel.app

        Review it and say "deploy to production" when ready.

You: deploy to production

Claude: 🚀 Deployed to production: https://my-app.vercel.app
```
