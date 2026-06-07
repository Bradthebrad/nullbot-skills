---
name: mcp-skill
description: Build, document, release, and marketplace-register Go MCP servers for NullBot and other MCP-compatible clients.
allowed-tools: market_list_available, market_read_package, market_install_package, mcp_list_servers, mcp_enable_server, mcp_disable_server, mcp_remove_server, config_dir_list, config_dir_read, skills_list, history_recent, logs_recent
---

# MCP Skill

Use this skill when the user asks you to create, modify, release, or marketplace-register an MCP server.

This skill gives guidance only. It does not grant file, shell, GitHub, or release powers. Use only the tools currently available to you, and ask the user to install/enable coding or GitHub MCP servers when needed.

## Core Principles

- MCP servers should be standalone and useful outside NullBot.
- Stdio should be the default local transport.
- Optional local network transports should include `streamable-http`, `http`, and `sse` when feasible.
- Dangerous capabilities must be explicit in README and marketplace metadata.
- Release assets should include a normal `.exe`, an optional UPX-compressed `-small.exe`, and `SHA256SUMS.txt`.
- Small UPX binaries must warn users about possible Windows AV or SmartScreen false positives.

## Recommended Go Shape

Use `tinychain/mcp` for minimal MCP servers:

```go
server := mcp.NewServer("example-mcp")
server.Version = "0.1.0"
server.AddTool(mcp.Tool{
    Name:        "example_tool",
    Description: "Describe exactly what this tool does.",
    InputSchema: map[string]any{
        "type": "object",
        "properties": map[string]any{
            "text": map[string]any{"type": "string", "description": "Input text."},
        },
        "required": []string{"text"},
    },
    Handler: func(ctx context.Context, args map[string]any) (mcp.ToolResult, error) {
        return mcp.Text("ok"), nil
    },
})
_ = server.Run(context.Background())
```

Provide flags:

- `--transport stdio|streamable-http|http|sse`
- `--addr 127.0.0.1:<port>`
- `--path /mcp`
- `--sse-path /sse`
- `--message-path /message`
- `--version`

## Tool Design Checklist

For each tool:

- use a clear, stable snake_case name;
- write a precise description;
- use strict JSON schema-like input fields;
- make destructive behavior opt-in;
- return JSON-shaped text when structure matters;
- cap large output;
- include useful error messages;
- avoid absolute paths unless there is a deliberate permission model;
- document required permissions.

## Repo Checklist

Create:

- `go.mod`
- `cmd/<server-name>/main.go`
- `internal/<domain>/...`
- `README.md`
- `.gitignore`
- tests for core tool behavior

README should include:

- what the server does;
- tool table;
- safety/permission model;
- build command;
- stdio usage;
- HTTP/SSE usage;
- generic MCP client config example;
- development/test commands.

## Build Commands

Normal release build:

```powershell
go build -trimpath -ldflags "-s -w" -o dist\releases\v0.1.0\server-name.exe .\cmd\server-name
```

Optional UPX small build:

```powershell
Copy-Item dist\releases\v0.1.0\server-name.exe dist\releases\v0.1.0\server-name-small.exe
C:\tmp\upx-5.1.1-win64\upx.exe --best --lzma dist\releases\v0.1.0\server-name-small.exe
```

Checksums:

```powershell
Get-FileHash dist\releases\v0.1.0\*.exe -Algorithm SHA256
```

## Release Checklist

- Run `go test ./...`.
- Build normal `.exe`.
- Build optional `-small.exe`.
- Smoke-test `--version`.
- Smoke-test stdio `tools/list`.
- Generate `SHA256SUMS.txt`.
- Write release notes with UPX warning if applicable.
- Publish GitHub release.
- Verify assets are visible.

## Marketplace Metadata

A marketplace package should include:

- `id`
- `kind`: `mcp_server` or `skill_pack`
- `name`
- `description`
- `repo`
- `release_tag`
- assets with URLs and checksums
- default transport
- default args
- permissions
- install/enabled status

Use concise, user-facing permission names such as:

- `workspace_read`
- `workspace_write`
- `command_execute`
- `document_parse`
- `network_fetch`
- `github_release_download`
- `skill_install`

## When Asked To Build A New MCP Server

1. Clarify the server's domain and permission level if it is risky.
2. Inspect existing MCP examples when available.
3. Scaffold a small Go repo.
4. Implement the minimal useful tool suite.
5. Add tests.
6. Build and smoke-test.
7. Write README and release notes.
8. Publish release only when the user explicitly asks.
9. Suggest marketplace metadata updates after release.

Never claim a tool is installed, enabled, or published unless you have verified it with available tools.
