# Security

## API Keys & Credentials

This plugin **never stores, logs, or transmits API keys**. All credentials are handled by the platform CLIs directly:

- **Vercel:** `vercel login` — stored in `~/.local/share/com.vercel.cli/`
- **Railway:** `railway login` — stored in `~/.railway/config.json`
- **Fly.io:** `fly auth login` — stored in `~/.fly/config.yml`

The plugin only runs CLI commands on your behalf. No credentials are sent to any third-party service by this plugin.

## Dashboard UI

The `ui/index.html` dashboard is a static file served locally. It:
- Contains no server-side code
- Does not make network requests
- Does not store any data
- Does not load external resources except Tailwind CSS CDN (pinned version)

## Reporting Vulnerabilities

Please report security issues to the repository maintainer via GitHub Security Advisories (private disclosure).

Do **not** open public issues for security vulnerabilities.
