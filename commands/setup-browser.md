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

2. Determine the `.mcp.json` file location. Use the current working directory. If a `.mcp.json` already exists, merge the `playwright-ext` server entry into the existing `mcpServers` object without overwriting other servers.

3. Create or update `.mcp.json` with the following configuration:

```json
{
  "mcpServers": {
    "playwright-ext": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--extension"],
      "env": {
        "PLAYWRIGHT_MCP_EXTENSION_TOKEN": "<TOKEN>"
      }
    }
  }
}
```

4. After writing the file, inform the user:
   - Configuration saved successfully
   - **Restart Claude Code** for the new MCP server to connect
   - After restart, use `mcp__playwright-ext__*` tools to operate on the existing browser
   - The Chrome extension should show "MCP client connected" after restart

## Important Notes

- Do NOT place this config inside a plugin's `.mcp.json` — plugin-level env vars are not reliably passed to MCP processes
- The project-level `.mcp.json` (in the working directory or home directory) is the correct location
- If the user already has a `.mcp.json` with other servers, preserve those entries
