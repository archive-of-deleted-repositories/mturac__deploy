# SKILL.md

name: detect-platform
plugin: deploy
description: >
  Detects the project's framework, build configuration, and deployment target
  platform by inspecting package.json, config files, and project structure.
  Outputs a structured detection report to inform subsequent deploy steps.

---

# Detect Platform

## Purpose

Before deploying any project, Claude must understand what framework it uses, how it builds, and where it should be deployed. This skill teaches Claude how to systematically inspect a project and produce a structured detection report.

## Instructions

Follow these steps in order. Do not skip any step.

### Step 1: Read package.json

Read the `package.json` file at the project root. Extract and note:

- **name** — the project name
- **scripts** — specifically `build`, `start`, `dev`, `preview`, `export`, and `postinstall`
- **dependencies** — all production dependencies
- **devDependencies** — all development dependencies
- **engines** — if present, the Node version constraint

If `package.json` does not exist, note that the project is **not a Node.js project** and skip to Step 1b.

#### Step 1b: Non-Node Detection

Check for these files to identify non-Node frameworks:

| File(s) | Framework |
|---------|-----------|
| `requirements.txt` or `pyproject.toml` or `Pipfile` | Python project |
| `go.mod` or `go.sum` | Go project |
| `Gemfile` | Ruby project |
| `Cargo.toml` | Rust project |
| `*.html` files only, no build system | Plain static HTML |

For Python projects, check for: `fastapi`/`uvicorn` → Python/FastAPI, `flask` → Python/Flask, `django` → Python/Django.

---

### Step 2: Detect Framework

Check dependencies in this priority order:

| Detection Logic | Framework |
|-----------------|-----------|
| `next` in deps | **Next.js** |
| `nuxt` in deps or `nuxt.config.ts` | **Nuxt** |
| `@sveltejs/kit` in deps | **SvelteKit** |
| `@remix-run/react` in deps | **Remix** |
| `astro` in deps | **Astro** |
| `vite` in devDeps (none above) | **Vite** |
| `react-scripts` in deps | **Create React App** |
| `express` in deps | **Express (Node API)** |
| `fastify` in deps | **Fastify (Node API)** |
| `hono` in deps | **Hono** |

---

### Step 3: Determine Build Command and Output Directory

| Framework | Build Command | Output Dir |
|-----------|--------------|------------|
| Next.js | `npm run build` | `.next` |
| Nuxt | `npm run build` | `.output` |
| SvelteKit | `npm run build` | `build` |
| Remix | `npm run build` | `build` |
| Astro | `npm run build` | `dist` |
| Vite | `npm run build` | `dist` |
| Create React App | `npm run build` | `build` |
| Express/Fastify | `node index.js` | root |
| Python/FastAPI | N/A | N/A |
| Go | `go build -o ./bin/app` | `bin` |

**Override:** Always use the exact `scripts.build` value from package.json if it exists. Detect package manager from lockfile: `pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, `bun.lockb` → bun.

---

### Step 4: Detect Deployment Target Platform

Check for config files:

| File | Platform |
|------|----------|
| `vercel.json` or `.vercel/` | **Vercel** |
| `railway.json` or `railway.toml` | **Railway** |
| `fly.toml` or `.fly/` | **Fly.io** |
| `render.yaml` or `render.yml` | **Render** |
| `Dockerfile` only | **Docker** (recommend Fly.io or Railway) |

---

### Step 5: Recommend Platform If None Detected

| Framework | Recommended |
|-----------|-------------|
| Next.js, Nuxt, Remix | **Vercel** |
| Astro, SvelteKit, Vite, CRA | **Vercel** |
| Express, Fastify, Hono | **Railway** or **Fly.io** |
| Python/FastAPI | **Railway** or **Render** |
| Go | **Fly.io** |
| Dockerfile | **Fly.io** |

---

### Step 6: Required CLI Tool

| Platform | CLI | Install |
|----------|-----|---------|
| Vercel | `vercel` | `npm i -g vercel` |
| Fly.io | `flyctl` | `brew install flyctl` |
| Railway | `railway` | `npm i -g @railway/cli` |
| Render | Dashboard only | N/A |

Run `which <cli>` to check if already installed.

---

### Step 7: Output Detection Report

```
## 🔍 Platform Detection Report

### Project
- **Name:** <name>
- **Framework:** <framework>
- **Evidence:** <what proved it>

### Build Configuration
- **Build Command:** <command>
- **Output Directory:** <dir>
- **Package Manager:** <npm/yarn/pnpm/bun>

### Deployment Target
- **Detected Config:** <file or "None">
- **Platform:** <platform or "Recommended: X">
- **Reason:** <why>

### CLI Requirement
- **Tool:** <cli>
- **Installed:** <yes/no>
- **Install:** <command if not installed>

### Notes
- <warnings, conflicts, missing config>
```

---

## Edge Cases

- **Monorepo:** Scan each workspace, flag which is the deploy target
- **SSR frameworks:** Must deploy to serverless/persistent server, not static hosting
- **Multiple platform configs:** Report all, flag conflict for user input

## Trigger Phrases

Invoke when user says: "deploy", "where should I deploy", "detect framework", "prepare for deployment", "check deploy config"
