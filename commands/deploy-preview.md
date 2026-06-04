# Deploy Preview

Deploy a preview (staging) version of this project.

## Usage

```
/deploy-preview [platform]
```

## What this does

1. Detects platform (or uses the one you specify)
2. Vercel: runs `vercel`
3. Railway: runs `railway up`
4. Fly.io: runs `fly deploy` to a non-production app

Returns a preview URL for review before promoting to production.
