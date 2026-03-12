---
name: setup-browser
description: Configure Playwright to connect to your existing Chrome browser with login state
argument-hint: <PLAYWRIGHT_MCP_EXTENSION_TOKEN>
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
---

# Setup Browser Connection

Configure the Playwright MCP server to connect to the user's existing Chrome browser via the Playwright MCP Bridge extension.

## Instructions

1. The user provides the `PLAYWRIGHT_MCP_EXTENSION_TOKEN` as an argument. If no token is provided, ask the user to:
   - Install the [Playwright MCP Bridge](https://chromewebstore.google.com/detail/playwright-mcp-bridge/mmlmfjhmonkocbjadbfplnigmagldckm) Chrome extension
   - Click the extension icon in Chrome to view the token
   - Copy and provide the token

2. Create or update three locations with the token:

   a. **User token storage** (for SessionStart hook):
      - Path: `~/.claude/plugins/playwright-browser-connector/token.txt`
      - Content: just the token string

   b. **User-level .mcp.json** (persistent config, survives reloads):
      - Path: `~/.mcp.json`
      - Merge playwright-ext into existing mcpServers if present

   c. **Plugin cache .mcp.json** (immediate effect, patched on reload):
      - Find path: `~/.claude/plugins/cache/playwright-browser-connector/playwright-browser-connector/*/`
      - Directly patch the PLAYWRIGHT_MCP_EXTENSION_TOKEN value

3. After writing the files, inform the user:
   - Configuration saved successfully
   - **Restart Claude Code** for the new MCP server to connect
   - After restart, use `mcp__playwright-ext__*` tools to operate on the existing browser
   - The Chrome extension should show "MCP client connected" after restart

## Important Notes

- The SessionStart hook automatically patches the plugin cache on every session start, so `/reload-plugins` won't break the connection
- The user-level `~/.mcp.json` serves as the persistent token storage
- If the user already has a `.mcp.json` with other servers, preserve those entries
