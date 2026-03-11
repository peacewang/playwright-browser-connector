---
name: Browser Connector
description: This skill should be used when the user asks to "connect to existing browser", "use my Chrome login state", "operate my current browser", "reuse browser session", "connect Playwright to Chrome", or wants Playwright to control their already-open Chrome browser instead of launching a new one.
version: 1.0.0
---

# Browser Connector

Connect Playwright MCP to an existing Chrome browser window, preserving login state, cookies, and session data. This eliminates the default behavior of launching a new, clean browser instance.

## How It Works

The standard Playwright MCP server (`@playwright/mcp`) launches a new browser window with no login state. By using `--extension` mode together with the **Playwright MCP Bridge** Chrome extension, the MCP server connects to and controls the user's already-open Chrome browser.

**Architecture:**

```
Claude Code → playwright-ext MCP server (--extension mode)
                    ↓
         Playwright MCP Bridge Chrome Extension (via token auth)
                    ↓
         User's existing Chrome browser (with login state)
```

## Prerequisites

1. **Chrome browser** with the [Playwright MCP Bridge](https://chromewebstore.google.com/detail/playwright-mcp-bridge/mmlmfjhmonkocbjadbfplnigmagldckm) extension installed
2. **Extension token**: Open the extension popup in Chrome to obtain the `PLAYWRIGHT_MCP_EXTENSION_TOKEN`
3. **Node.js / npx** available in the system PATH

## Setup

Run the `/setup-browser` command with the token to configure automatically. Or manually create/edit the project-level `.mcp.json`:

```json
{
  "mcpServers": {
    "playwright-ext": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--extension"],
      "env": {
        "PLAYWRIGHT_MCP_EXTENSION_TOKEN": "<your-token-here>"
      }
    }
  }
}
```

After configuration, restart Claude Code for the MCP server to connect.

## Using the Correct Tools

Once connected, two sets of Playwright tools coexist:

| Tool prefix | Source | Browser |
|-------------|--------|---------|
| `mcp__playwright-ext__*` | `.mcp.json` (extension mode) | **Existing Chrome** with login state |
| `mcp__plugin_playwright_playwright__*` | Plugin system | New browser window (no login) |

**Always use `mcp__playwright-ext__*` tools** to operate on the user's existing browser. Full tool list (22 tools):

| Tool | Function |
|------|----------|
| `browser_navigate` | Navigate to a URL |
| `browser_navigate_back` | Go back in history |
| `browser_snapshot` | Capture page accessibility snapshot |
| `browser_take_screenshot` | Take screenshot |
| `browser_click` | Click elements |
| `browser_type` | Type text into elements |
| `browser_fill_form` | Fill multiple form fields |
| `browser_press_key` | Press keyboard keys |
| `browser_select_option` | Select dropdown options |
| `browser_hover` | Hover over elements |
| `browser_drag` | Drag and drop elements |
| `browser_tabs` | List/create/close/select tabs |
| `browser_evaluate` | Execute JavaScript on page |
| `browser_run_code` | Run Playwright code snippet |
| `browser_file_upload` | Upload files |
| `browser_handle_dialog` | Handle browser dialogs |
| `browser_console_messages` | Get console messages |
| `browser_network_requests` | Get network requests |
| `browser_wait_for` | Wait for text/condition |
| `browser_resize` | Resize browser window |
| `browser_close` | Close the page |
| `browser_install` | Install browser binary |

## Workflow

1. Verify the extension shows "MCP client connected" in Chrome
2. Use `mcp__playwright-ext__browser_snapshot` to see the current page state
3. Use `mcp__playwright-ext__browser_navigate` to go to any URL (login state preserved)
4. Interact with pages using click, type, fill, and other tools

## Troubleshooting

### Extension shows "No MCP clients connected"

- Verify the token in `.mcp.json` matches the token shown in the extension popup
- Restart Claude Code after updating `.mcp.json`
- Check that `npx @playwright/mcp@latest --extension` is runnable from the terminal

### Plugin system `.mcp.json` env not working

The Claude Code plugin system's `.mcp.json` does NOT reliably pass `env` variables to MCP server processes. The workaround is to use a **project-level** `.mcp.json` file (at the working directory root or `~/.mcp.json`) which does support `env` properly.

### Still opens a new browser window

Make sure to use `mcp__playwright-ext__*` tools, NOT `mcp__plugin_playwright_playwright__*` tools. The plugin system's Playwright always opens a new window.

### Token changed

If the Chrome extension generates a new token (e.g., after reinstall), update the token in `.mcp.json` and restart Claude Code.
