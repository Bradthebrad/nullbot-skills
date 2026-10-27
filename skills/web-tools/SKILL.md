---
name: web-tools
description: Use NullBot Web MCP safely for web search, URL fetching/readability, and Chromium browser automation via localhost CDP.
allowed-tools: web_workspace_info, list_search_providers, web_search, fetch_url, browser_status, browser_attach, browser_launch, browser_navigate, browser_read, browser_query, browser_click, browser_type, browser_screenshot, browser_tabs, browser_eval
---

# Web Tools

Use this skill when a task needs current public web information, page reading, or controlled browser automation through the `nullbot-web-mcp` server.

This skill gives guidance only. It does not grant network, browser, file, or secret access by itself. Use only tools currently available to you.

## Required MCP Server

Expected MCP server: `nullbot-web-mcp`.

Start by checking `web_workspace_info` when you need to know:

- whether provider keys are present;
- which search provider is selected;
- current workspace/output policy;
- configured CDP endpoint and browser state;
- whether `browser_eval` is enabled;
- max fetch/read caps and available tool names.

## Tool Groups

### Search and fetch

Use these first for normal research and source reading:

- `list_search_providers` — inspect available providers and key presence without exposing secrets.
- `web_search` — search through the configured provider, currently Brave when `BRAVE_API_KEY` is available.
- `fetch_url` — fetch safe public `http`/`https` URLs and return readability, text, raw, or header-focused output.

### Browser automation

Use browser tools when static fetch/search is insufficient:

- pages require JavaScript rendering;
- you need DOM structure, element text, screenshots, or tab control;
- the user explicitly asks for browser interaction.

Browser tools include `browser_status`, `browser_launch`, `browser_attach`, `browser_navigate`, `browser_read`, `browser_query`, `browser_click`, `browser_type`, `browser_screenshot`, `browser_tabs`, and gated `browser_eval`.

## Decision Guide

Prefer the least powerful tool that can answer the question:

1. Use `web_search` to discover candidate sources.
2. Use `fetch_url` to read public pages and cite exact URLs.
3. Use browser read/query only when fetch output is insufficient or the page needs rendering.
4. Use click/type/tab/eval only when the user asked for interaction and the action is safe.

Do not use browser automation for simple public text pages that `fetch_url` can read.

## Safety Rules

- Treat browser interaction as `browser_control` capability.
- Confirm before purchases, sends, deletes, account changes, private-data submission, consequential forms, or other consequential actions.
- Do not bypass paywalls, logins, access controls, CAPTCHAs, rate limits, or robots/terms restrictions.
- Do not inspect or manipulate private sessions unless the user explicitly asks and the task is authorized.
- Do not expose API keys or secrets; provider status should only report key presence.
- `fetch_url` is for public `http`/`https`; expect private/local/network targets to be rejected.
- Screenshots must use workspace-relative `.png` paths.
- `browser_eval` must be disabled unless `--allow-eval=true`; use it only for bounded inspection and avoid destructive scripts.

## Reporting

When reporting web-derived answers, include:

- source URLs used;
- whether content came from search results, fetch/readability, or browser rendering;
- any skipped live checks due to missing provider keys, missing browser, or disabled eval;
- limitations when sources are incomplete or dynamic content could not be inspected.
