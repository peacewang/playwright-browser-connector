# Playwright Browser Connector

[English](README_EN.md) | **中文**

让 Playwright 连接到你已打开的 Chrome 浏览器，直接复用登录状态，而不是每次都打开一个全新的浏览器窗口。

## 功能特性

- 复用 Chrome 浏览器的登录会话、Cookie 和本地存储
- 无需在网站上重新登录
- 通过 Claude Code 直接控制你正在使用的浏览器标签页
- 完整的 Playwright 能力：22 个工具，包括导航、点击、输入、截图、执行脚本、拖拽、标签管理、网络请求检查等
- 跨平台支持：Windows、macOS（Intel 和 Apple Silicon）、Linux

## 安装

### 从 GitHub 安装

```bash
# 1. 添加 marketplace
claude plugin marketplace add peacewang/playwright-browser-connector

# 2. 安装插件
claude plugin install playwright-browser-connector
```

### 克隆到本地安装

```bash
# 1. 克隆仓库
git clone https://github.com/peacewang/playwright-browser-connector.git

# 2. 添加本地 marketplace
claude plugin marketplace add /path/to/playwright-browser-connector

# 3. 安装插件
claude plugin install playwright-browser-connector
```

### 临时加载（不安装）

```bash
claude --plugin-dir /path/to/playwright-browser-connector
```

### 更新插件

```bash
claude plugin update playwright-browser-connector
```

> 仓库有新的代码推送即可更新，无需打 tag 或发 release。更新后重启 Claude Code 生效。

## 环境依赖

| 依赖项               | 说明                                   |
| ----------------- | ------------------------------------ |
| **Node.js** (≥18) | 需要 `npx` 命令可用，用于启动 MCP 服务器           |
| **Chrome 浏览器**    | 需要安装 Playwright MCP Bridge 扩展        |
| **网络连接**          | 首次运行时 `npx` 需要下载 `@playwright/mcp` 包 |

> Claude Code 本身不算额外依赖（它是运行环境）。除以上三项外，无其他环境依赖。无需安装 Python、Docker、数据库或其他任何工具。

## 使用方式

### 自动触发（Skill）

安装并配置完成后，在 Claude Code 中用自然语言描述需求即可自动触发插件，例如：

- "帮我打开我的 Twitter，总结最近的推文"
- "用我的浏览器登录态访问 GitHub"
- "连接到我当前的 Chrome 浏览器"
- "用我已有的登录状态打开某个网站"
- "操作我当前打开的浏览器页面"

Claude 会自动识别意图并使用 `mcp__playwright-ext__*` 工具操作你已打开的 Chrome 浏览器。

### 命令触发

| 命令                       | 说明                                         |
| ------------------------ | ------------------------------------------ |
| `/setup-browser <token>` | 配置 Playwright MCP Bridge 连接，写入 `.mcp.json` |

### 注意事项

- 插件触发后，Claude 使用的是 `mcp__playwright-ext__*` 系列工具（连接已有浏览器），而非 `mcp__plugin_playwright_playwright__*`（打开新浏览器）
- 首次配置后需要重启 Claude Code

## 快速上手

### 1. 安装 Chrome 扩展

在 Chrome 中安装 [Playwright MCP Bridge](https://chromewebstore.google.com/detail/playwright-mcp-bridge/mmlmfjhmonkocbjadbfplnigmagldckm) 扩展。

### 2. 获取 Token

点击 Chrome 工具栏中的扩展图标，复制显示的 `PLAYWRIGHT_MCP_EXTENSION_TOKEN`。

### 3. 配置

在 Claude Code 中运行 setup 命令：

```
/setup-browser <你的token>
```

或者手动在工作目录或用户主目录下创建 `.mcp.json` 文件：

```json
{
  "mcpServers": {
    "playwright-ext": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--extension"],
      "env": {
        "PLAYWRIGHT_MCP_EXTENSION_TOKEN": "<你的token>"
      }
    }
  }
}
```

### 4. 重启 Claude Code

重启后，Chrome 扩展应显示 "MCP client connected"。

## 工作原理

标准的 Playwright MCP 会打开一个全新的浏览器窗口。本插件使用 `@playwright/mcp --extension` 模式，通过 Chrome 扩展连接到你已经打开的浏览器，从而保留所有登录状态。

```
Claude Code → playwright-ext MCP 服务器 (--extension 模式)
                    ↓
         Playwright MCP Bridge Chrome 扩展 (通过 token 认证)
                    ↓
         用户已打开的 Chrome 浏览器 (保留登录状态)
```

## 可用工具 (22 个)

所有工具通过 `mcp__playwright-ext__` 前缀调用：

| 工具                         | 功能                 |
| -------------------------- | ------------------ |
| `browser_navigate`         | 导航到指定 URL          |
| `browser_navigate_back`    | 返回上一页              |
| `browser_snapshot`         | 获取页面无障碍快照          |
| `browser_take_screenshot`  | 页面截图               |
| `browser_click`            | 点击元素               |
| `browser_type`             | 输入文本               |
| `browser_fill_form`        | 批量填写表单             |
| `browser_press_key`        | 按下键盘按键             |
| `browser_select_option`    | 选择下拉菜单选项           |
| `browser_hover`            | 悬停在元素上             |
| `browser_drag`             | 拖放元素               |
| `browser_tabs`             | 管理浏览器标签页           |
| `browser_evaluate`         | 在页面中执行 JavaScript  |
| `browser_run_code`         | 运行 Playwright 代码片段 |
| `browser_file_upload`      | 上传文件               |
| `browser_handle_dialog`    | 处理浏览器对话框           |
| `browser_console_messages` | 获取控制台消息            |
| `browser_network_requests` | 获取网络请求             |
| `browser_wait_for`         | 等待文本或条件            |
| `browser_resize`           | 调整浏览器窗口大小          |
| `browser_close`            | 关闭页面               |
| `browser_install`          | 安装浏览器二进制文件         |

## 平台支持

| 平台                                | 状态  |
| --------------------------------- | --- |
| Windows 10/11                     | 支持  |
| macOS (Intel)                     | 支持  |
| macOS (Apple Silicon M1/M2/M3/M4) | 支持  |
| Linux                             | 支持  |

本插件仅依赖 `npx`（Node.js）和 Chrome 扩展，无任何原生二进制文件或平台特定代码。

## 常见问题

### 扩展显示 "No MCP clients connected"

- 确认 `.mcp.json` 中的 token 与扩展弹窗中显示的一致
- 更新 `.mcp.json` 后需要重启 Claude Code
- 确认终端中可以运行 `npx @playwright/mcp@latest --extension`

### 还是打开了新的浏览器窗口

确保使用的是 `mcp__playwright-ext__*` 系列工具，而不是 `mcp__plugin_playwright_playwright__*`。后者是插件系统自带的 Playwright，始终会打开新窗口。

### Token 变了

如果 Chrome 扩展重新生成了 token（例如重新安装扩展后），更新 `.mcp.json` 中的 token 并重启 Claude Code。

## 许可证

MIT
