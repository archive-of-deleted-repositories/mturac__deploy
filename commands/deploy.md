# Deploy

Deploy this project to a hosting platform.

## Usage

```
/deploy [platform] [environment]
```

## What this does

1. Runs the `detect-platform` skill to identify your framework, build config, and target platform
2. Checks CLI tools are installed and authenticated
3. Runs a preview deploy first (unless `--prod` is specified)
4. Reports the deploy URL when complete

## Examples

- `/deploy` — auto-detect platform and run preview deploy
- `/deploy vercel` — deploy to Vercel
- `/deploy railway` — deploy to Railway
- `/deploy fly` — deploy to Fly.io
- `/deploy render` — show render.yaml setup and git-push instructions
- `/deploy --prod` — skip preview, deploy directly to production (requires confirmation)

## Notes

- Always runs preview first by default — you confirm before going to production
- Checks for uncommitted changes and warns before deploying
- Checks for environment variables that may need to be set on the platform
