---
name: browser-automation
description: Safely use NullBot Web MCP Chromium/CDP browser tools for status, launch/attach, navigation, reading, DOM interaction, screenshots, tabs, and gated eval.
allowed-tools: web_workspace_info, browser_status, browser_launch, browser_attach, browser_navigate, browser_read, browser_query, browser_click, browser_type, browser_screenshot, browser_tabs, browser_eval
---

# Browser Automation

Use this skill when a task requires a real Chromium-family browser through `nullbot-web-mcp`, especially for JavaScript-rendered pages, DOM querying, screenshots, tab management, or user-authorized interactions.

This skill gives guidance only. It does not grant browser tools by itself.

## Safety First

Browser tools are `browser_control` capability. Confirm with the user before:

- purchases or payments;
- sends, posts, submissions, or messages;
- deletes or irreversible changes;
- account/profile/security changes;
- private-data entry or submission;
- consequential forms or workflows.

Do not bypass login, paywalls, CAPTCHAs, access controls, or site rules. Avoid interacting with private sessions unless the user explicitly requests it and the task is authorized.

## Basic Flow

1. Call `browser_status` to inspect endpoint reachability, attach state, active tab, and guidance.
2. If no browser is available, either:
   - ask the user to start Chromium with remote debugging on localhost, or
   - use `browser_launch` only when opening a local browser is appropriate for the task.
3. Use `browser_attach` after the endpoint is reachable.
4. Use `browser_navigate` for `http`/`https` pages.
5. Use read/query tools before mutating tools.

## Launch and Attach

`browser_launch` is opt-in. It should bind remote debugging to `127.0.0.1` and may use:

- an isolated temporary profile by default;
- a provided `browser_path` when auto-detection fails;
- a provided `profile_dir` only when the user accepts real-profile/session risks.

Prefer temporary profiles for research. Warn that persistent profiles can retain cookies, sessions, history, downloads, and account state.

## Reading and Querying

Use `browser_read` modes:

- `text` for visible text;
- `readability` for page-like content distilled from HTML;
- `outline` for headings/links/forms/buttons structure;
- `html` only when markup inspection is needed.

Use `browser_query` for CSS selectors. It returns capped matches with indexes, text, selected attributes, visibility/disabled/checked hints, and bounding boxes where possible.

## Clicking and Typing

Use `browser_click` and `browser_type` only after inspecting the target with `browser_query` or `browser_read`.

- Prefer CSS selectors over coordinates when possible.
- Handle no-match and multiple-match errors by querying more specifically.
- Do not type secrets/private data unless the user explicitly provides and authorizes that use.
- Do not submit forms or press Enter for consequential actions without confirmation.

## Screenshots

Use `browser_screenshot` for evidence or visual inspection.

- Output path must be workspace-relative.
- Use `.png` extension.
- Choose viewport versus full-page based on the task.
- Mention the saved path in the response.

## Tabs

Use `browser_tabs` to list, select, open, and close tabs. Avoid closing user tabs unexpectedly. The tool protects against closing the last known page target unless forced; use `force` only with clear intent.

## Eval Gate

`browser_eval` is disabled unless the server was started with `--allow-eval=true`.

When enabled:

- keep expressions short and inspection-focused;
- do not run destructive scripts;
- do not exfiltrate secrets or private page data;
- prefer read/query tools before eval.

If disabled, explain that eval is gated and use safer read/query alternatives.
