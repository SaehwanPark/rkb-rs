---
title: "Model Context Protocol (MCP) Integration Guide"
---

# 🤖 Model Context Protocol (MCP) Integration Guide

`rkb-rs` includes a built-in **Model Context Protocol (MCP)** server, allowing AI assistants like **Claude Desktop**, **Claude Code**, **Google Antigravity**, and **Codex** to directly query your local ResDAC CMS documentation archive.

---

## 🔌 Available MCP Tools

When `rkb` runs as an MCP server, it provides the following tools:

| MCP Tool Name | Description | Parameters |
| :--- | :--- | :--- |
| `search_datasets` | Search CMS dataset listings and file categories. | `query` (string), `limit` (int) |
| `search_documents` | Search documentation guides, manuals, and briefs. | `query` (string), `limit` (int) |
| `search_variables` | Search CMS variable definitions, aliases, and types. | `query` (string), `limit` (int) |
| `search_chunks` | Search granular parsed text segments with page numbers. | `query` (string), `limit` (int) |
| `get_agent_context` | Formats comprehensive citation-bearing context for an LLM prompt. | `query` (string), `limit` (int) |

---

## ⚡ Automated Client Setup (`rkb mcp-setup`)

`rkb` can automatically configure your local AI clients:

```bash
# Setup Claude Desktop
rkb mcp-setup --client claude-desktop

# Setup Claude Code (Project level)
rkb mcp-setup --client claude-code-project --project-path .

# Setup Google Antigravity
rkb mcp-setup --client antigravity --project-path .

# Setup Codex
rkb mcp-setup --client codex-project --project-path .

# Dry-run to preview configuration without writing
rkb mcp-setup --client claude-desktop --dry-run
```

---

## ⚙️ Manual Configuration

To manually configure an MCP client, add `rkb mcp` to your client's configuration file:

### Claude Desktop (`claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "rkb": {
      "command": "rkb",
      "args": ["mcp"]
    }
  }
}
```

### Antigravity / Claude Code (`.mcp.json` or project settings):
```json
{
  "mcpServers": {
    "rkb": {
      "command": "rkb",
      "args": ["mcp"]
    }
  }
}
```

---

## 🧪 Testing the MCP Server Directly

The MCP server communicates over standard input/output (stdio) using line-delimited JSON-RPC 2.0.

Test tool listing:
```bash
echo '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}' | rkb mcp
```

Test calling `get_agent_context`:
```bash
echo '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"get_agent_context","arguments":{"query":"BENE_ID"}}}' | rkb mcp
```

### Lifecycle Commands:
`rkb` also supports lifecycle state recording for persistent orchestrators:
```bash
rkb mcp start --host 127.0.0.1 --port 9000
rkb mcp status
rkb mcp stop
```
*Note: The foreground stdio mode (`rkb mcp`) is the verified, primary protocol transport.*
