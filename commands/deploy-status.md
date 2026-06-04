# Deploy Status

Check the status of recent deployments.

## Usage

```
/deploy-status [platform]
```

## What this does

- Vercel: runs `vercel ls` to show recent deployments
- Railway: runs `railway status` and `railway deployments`
- Fly.io: runs `fly status` and `fly releases`

Shows: last 5 deployments, their status (ready/error/building), URLs, and timestamps.
