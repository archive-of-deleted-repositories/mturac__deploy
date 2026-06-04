---
name: deploy-rollback
plugin: deploy
description: Roll back a deployment to the previous version on Vercel, Railway, or Fly.io. Always checks for database migration safety before rolling back.
triggers:
  - "rollback"
  - "revert deploy"
  - "undo deployment"
  - "go back to previous version"
  - "something broke after deploy"
---

# Deploy Rollback

## ⚠️ Pre-Rollback Safety Check — MANDATORY

Before rolling back ANY deployment, run:

```bash
git log --oneline -5
git diff HEAD~1 HEAD -- '**/migrations/**' '*schema*'
```

**If database migrations exist in the last deploy:**
> "⚠️ This deploy contains database migrations. Rolling back the code WITHOUT rolling back the database may cause schema mismatches or data corruption.
>
> Options:
> 1. Write and run a reverse migration before rolling back
> 2. Deploy a hotfix forward instead of rolling back
> 3. Proceed if the migration is additive-only (adding columns, not removing)
>
> **Wait for explicit user confirmation before proceeding.**"

---

## Vercel Rollback

```bash
# Option 1: Instant rollback CLI
vercel rollback

# Option 2: Re-deploy previous commit
git revert HEAD --no-edit && git push origin main
```

Or: Vercel Dashboard → Deployments → previous deploy → "Promote to Production"

---

## Railway Rollback

```bash
railway deployments
railway redeploy <deployment-id>
```

---

## Fly.io Rollback

```bash
fly releases list
fly deploy --image registry.fly.io/<app>:<previous-version>
```

---

## Universal Git Rollback

```bash
# Revert the last commit (safe, preserves history)
git revert HEAD --no-edit
git push origin main

# Revert a specific commit
git revert <bad-sha> --no-edit
git push origin main
```

**Always prefer `git revert` — it preserves history and is safe for shared branches.**

---

## Post-Rollback Verification

1. Check deployment succeeded (`vercel ls` / `fly status` / `railway status`)
2. Verify issue resolved (logs, health endpoint, error rates)
3. Plan a forward fix — don't stay on a rolled-back version

---

## Guardrails

- Always check for DB migrations before rolling back
- Prefer rolling forward (hotfix) when migrations are involved
- Verify service health after rollback completes
