---
name: deploy-fly
plugin: deploy
description: Deploy to Fly.io using flyctl. Handles app initialization, Dockerfile setup, deployments, scaling, and secrets.
triggers:
  - "deploy to fly"
  - "fly deploy"
  - "deploy to fly.io"
  - "flyctl deploy"
---

# Deploy to Fly.io

## Prerequisites

```bash
which flyctl
# If missing: brew install flyctl
# Or: curl -L https://fly.io/install.sh | sh
fly auth whoami
# If not logged in: fly auth login → browser
```

---

## Step 1: Initialize App

```bash
ls fly.toml 2>/dev/null
```

If `fly.toml` not found:
```bash
fly launch
```

Prompts:
| Prompt | Answer |
|--------|--------|
| App name | Accept or set custom |
| Region | Choose nearest (e.g., `iad` US East, `fra` Europe) |
| Set up Postgres? | Only if needed |
| Deploy now? | **N** — review config first |

This creates `fly.toml` and optionally a `Dockerfile`.

---

## Step 2: Review fly.toml

Ensure `fly.toml` has correct settings:
```toml
app = "<app-name>"
primary_region = "iad"

[build]
# If using Dockerfile (default)

[http_service]
  internal_port = 8080   # Port your app listens on
  force_https = true
  auto_stop_machines = true
  auto_start_machines = true
  min_machines_running = 0
```

**Critical:** Set `internal_port` to match the port your app binds to.

---

## Step 3: Deploy

```bash
fly deploy
```

Expected output:
```
==> Building image
--> Image built
==> Creating release
--> v1 deployed successfully
==> Monitoring deployment
1 desired, 1 placed, 1 healthy, 0 unhealthy
--> v1 deployed successfully
```

Get the URL:
```bash
fly open
```

---

## Secrets (Environment Variables)

```bash
# Set a secret
fly secrets set DATABASE_URL=<value>

# Set multiple
fly secrets set DATABASE_URL=<val> API_KEY=<val>

# List secrets (values hidden)
fly secrets list

# Remove
fly secrets unset DATABASE_URL
```

---

## Scaling

```bash
# Scale to 2 instances
fly scale count 2

# Set VM size
fly scale vm shared-cpu-1x  # or performance-1x, performance-2x

# Check current scale
fly status
```

---

## Logs

```bash
fly logs
fly logs --instance <id>
```

---

## Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `no Dockerfile found` | No Dockerfile in project | Run `fly launch` to generate one, or create manually |
| `port mismatch` | App binds to wrong port | Set `internal_port` in fly.toml to match app port |
| `health check failed` | App not responding on health path | Add health check endpoint or adjust `[checks]` in fly.toml |
| `Insufficient capacity` | Region out of capacity | Try `fly regions add <other-region>` |

---

## Guardrails

- Fly.io uses Machines (VMs) — no cold starts like serverless
- For stateful apps, use `fly volumes create` for persistent storage
- Always use `fly secrets set` for sensitive values — never put them in fly.toml
- `auto_stop_machines = true` saves cost for low-traffic apps but adds ~1s cold start
