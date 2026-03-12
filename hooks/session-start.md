---
name: playwright-ext-token-patcher
description: Patch playwright-ext MCP config with user's token on session start
trigger: SessionStart
---

# Playwright Extension Token Patcher

This hook runs on every Claude Code session start to ensure the playwright-ext MCP server has the correct token.

## How It Works

1. Read token from `~/.claude/plugins/playwright-browser-connector/token.txt` (created by /setup-browser)
2. Find the plugin cache directory for playwright-browser-connector
3. Patch the cached `.mcp.json` with the real token value

## Implementation

```javascript
// Read token
const tokenPath = path.join(os.homedir(), '.claude/plugins/playwright-browser-connector/token.txt');
const token = fs.readFileSync(tokenPath, 'utf-8').trim();

// Find and patch cache .mcp.json
const cacheDir = path.join(os.homedir(), '.claude/plugins/cache/playwright-browser-connector');
const versionDirs = fs.readdirSync(cacheDir).filter(d => d !== '.git');
const mcpJsonPath = path.join(cacheDir, versionDirs[0], versionDirs[0], '.mcp.json');

const config = JSON.parse(fs.readFileSync(mcpJsonPath, 'utf-8'));
config.mcpServers['playwright-ext'].env.PLAYWRIGHT_MCP_EXTENSION_TOKEN = token;
fs.writeFileSync(mcpJsonPath, JSON.stringify(config, null, 2));
```

## Notes

- Runs before MCP servers start
- Non-blocking: if token file missing, silently skip
- Works across reloads and updates
