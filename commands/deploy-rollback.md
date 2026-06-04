# Deploy Rollback

Roll back to the previous deployment. Safety-checks for database migrations first.

## Usage

```
/deploy-rollback [platform]
```

## What this does

1. Checks git history for DB migrations in the last deploy — warns if found
2. Requires explicit confirmation before proceeding
3. Vercel: runs `vercel rollback` or promotes previous deployment
4. Railway: runs `railway redeploy <previous-id>`
5. Fly.io: deploys previous image tag

## When to use

- After a bad deploy that's causing errors
- When you need to quickly restore the previous working state

## Safety

**If the last deploy contained database migrations, rolling back the code without a reverse migration can cause data corruption. This command will warn you and require explicit approval.**
