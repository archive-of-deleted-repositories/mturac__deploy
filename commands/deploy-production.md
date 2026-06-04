# Deploy to Production

Deploy this project to production. Requires confirmation.

## Usage

```
/deploy-production [platform]
```

## What this does

1. Checks for uncommitted changes — warns if any exist
2. Confirms: "Are you sure you want to deploy to production?" — requires explicit yes
3. Vercel: runs `vercel --prod`
4. Railway: runs `railway up` (Railway uses branch-based envs)
5. Fly.io: runs `fly deploy`

Returns the production URL when complete.

## Safety

This command will NOT proceed without explicit user confirmation. Always run `/deploy-preview` first.
