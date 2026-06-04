# Deploy UI

Open the deploy dashboard in your browser.

## Usage

```
/deploy-ui
```

## What this does

Opens `ui/index.html` from the plugin directory in your default browser.
The dashboard shows: platform status, framework detection results, recent deployments, and command shortcuts.

```bash
open "$(dirname $(which claude))/../lib/node_modules/@anthropic/claude-code/plugins/deploy/ui/index.html" 2>/dev/null || \
open ~/.claude/plugins/deploy/ui/index.html 2>/dev/null || \
echo "Open deploy/ui/index.html in your browser"
```
