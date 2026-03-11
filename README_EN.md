# Playwright Browser Connector

**English** | [中文](README.md)

Connect Playwright to your existing Chrome browser with login state, instead of opening a new browser window.

## Features

- Reuse your Chrome browser's login sessions, cookies, and local storage
- No need to re-authenticate on websites
- Control your actual browser tabs via Claude Code
- Full Playwright capabilities: 22 tools including navigate, click, type, screenshot, evaluate, drag, tabs, network inspection, and more
- Cross-platform: Windows, macOS (Intel & Apple Silicon), Linux

## Installation

### From GitHub

```bash
# 1. Add marketplace
claude plugin marketplace add peacewang/playwright-browser-connector

# 2. Install plugin
claude plugin install playwright-browser-connector
```

### From Local Directory

```bash
claude --plugin-dir /path/to/playwright-browser-connector
```

## Prerequisites

- **Node.js** (with `npx`) installed
- **Chrome browser**

## Quick Start

### 1. Install Chrome Extension

Install the [Playwright MCP Bridge](https://chromewebstore.google.com/detail/playwright-mcp-bridge/mmlmfjhmonkocbjadbfplnigmagldckm) extension in Chrome.

### 2. Get Token

Click the extension icon in Chrome to view your `PLAYWRIGHT_MCP_EXTENSION_TOKEN`.

### 3. Configure

Run the setup command in Claude Code:

```
/setup-browser <your-token>
```

Or manually create a `.mcp.json` file in your working directory or home directory:

```json
{
  "mcpServers": {
    "playwright-ext": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--extension"],
      "env": {
        "PLAYWRIGHT_MCP_EXTENSION_TOKEN": "<your-token>"
      }
    }
  }
}
```

### 4. Restart Claude Code

The extension should show "MCP client connected" after restart.

## How It Works

The standard Playwright MCP opens a new browser. This plugin uses `@playwright/mcp --extension` mode, which connects through the Chrome extension to your already-open browser, preserving all login state.

```
Claude Code → playwright-ext MCP server (--extension mode)
                    ↓
         Playwright MCP Bridge Chrome Extension (via token auth)
                    ↓
         User's existing Chrome browser (with login state)
```

## Available Tools (22)

All standard Playwright MCP tools are available through the `mcp__playwright-ext__` prefix:

| Tool                       | Function                            |
| -------------------------- | ----------------------------------- |
| `browser_navigate`         | Navigate to a URL                   |
| `browser_navigate_back`    | Go back in history                  |
| `browser_snapshot`         | Capture page accessibility snapshot |
| `browser_take_screenshot`  | Take screenshot                     |
| `browser_click`            | Click elements                      |
| `browser_type`             | Type text into elements             |
| `browser_fill_form`        | Fill multiple form fields           |
| `browser_press_key`        | Press keyboard keys                 |
| `browser_select_option`    | Select dropdown options             |
| `browser_hover`            | Hover over elements                 |
| `browser_drag`             | Drag and drop elements              |
| `browser_tabs`             | List/create/close/select tabs       |
| `browser_evaluate`         | Execute JavaScript on page          |
| `browser_run_code`         | Run Playwright code snippet         |
| `browser_file_upload`      | Upload files                        |
| `browser_handle_dialog`    | Handle browser dialogs              |
| `browser_console_messages` | Get console messages                |
| `browser_network_requests` | Get network requests                |
| `browser_wait_for`         | Wait for text/condition             |
| `browser_resize`           | Resize browser window               |
| `browser_close`            | Close the page                      |
| `browser_install`          | Install browser binary              |

## Platform Support

| Platform                          | Status    |
| --------------------------------- | --------- |
| Windows 10/11                     | Supported |
| macOS (Intel)                     | Supported |
| macOS (Apple Silicon M1/M2/M3/M4) | Supported |
| Linux                             | Supported |

The plugin uses only `npx` (Node.js) and a Chrome extension — no native binaries or platform-specific code.

## Troubleshooting

- **Extension shows "No MCP clients connected"**: Verify the token matches, restart Claude Code
- **Still opens new browser**: Make sure to use `mcp__playwright-ext__*` tools, not `mcp__plugin_playwright_playwright__*`
- **Token changed**: Update token in `.mcp.json` and restart

## License

MIT
