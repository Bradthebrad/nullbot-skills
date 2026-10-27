---
name: browser-research
description: Combine web search, safe fetch/readability, browser rendering, screenshots, and citations for multi-step research or scrape-style tasks.
allowed-tools: web_workspace_info, list_search_providers, web_search, fetch_url, browser_status, browser_launch, browser_attach, browser_navigate, browser_read, browser_query, browser_screenshot, browser_tabs
---

# Browser Research

Use this skill for multi-step research, investigation, comparison, or scrape-style workflows that may combine search, URL fetch/readability, browser rendering, screenshots, and citations through `nullbot-web-mcp`.

This skill gives workflow guidance only. It does not grant tools by itself.

## Research Strategy

Use a staged approach:

1. Clarify the research question and required evidence level.
2. Check `web_workspace_info` or `list_search_providers` for search and browser availability.
3. Discover candidate sources with `web_search` when a provider key is available.
4. Fetch public pages with `fetch_url` and prefer readability/text for source extraction.
5. Use browser tools only for pages that require rendering, interaction, DOM inspection, or screenshots.
6. Synthesize findings with citations and state limitations.

## Source Discovery

When using `web_search`:

- query narrowly;
- prefer official/primary sources;
- diversify sources for comparisons;
- keep result counts modest;
- fetch pages before using claims.

If no provider key is present, work from user-provided URLs or explain that live search is unavailable.

## Fetch-First Reading

Use `fetch_url` before browser automation when possible. It is safer, faster, and easier to cite. Use it to collect:

- page title/content excerpts;
- publication/update dates when visible;
- canonical/final URL;
- status code and media type;
- clean readability text.

If fetch is blocked because the URL is private/local or unsafe, do not attempt to bypass the restriction.

## Browser Escalation

Escalate to browser tools when:

- important content is rendered by JavaScript;
- fetch output is empty/incomplete;
- visual confirmation is needed;
- a table/list requires DOM structure;
- screenshots are requested;
- the user explicitly asks for browser interaction.

Recommended browser flow:

1. `browser_status`
2. `browser_attach` or `browser_launch` if appropriate
3. `browser_navigate`
4. `browser_read` in `readability`, `text`, or `outline` mode
5. `browser_query` for targeted selectors
6. `browser_screenshot` for visual evidence when useful

Avoid mutating tools in research workflows unless the user explicitly asks and confirms the action.

## Evidence Capture

For each important source, record:

- URL and final URL;
- source title or page heading;
- relevant quoted/paraphrased facts;
- fetch/browser method used;
- date/time sensitivity if applicable;
- screenshot path if captured.

Screenshots must be saved as workspace-relative `.png` files and should be used only when they add evidence or help the user verify UI state.

## Citations and Synthesis

When answering:

- cite the specific URLs read;
- separate confirmed facts from inference;
- mention if source content came from fetch/readability or browser-rendered reading;
- identify stale, conflicting, or missing information;
- avoid overclaiming from snippets or partial pages.

## Safety Boundaries

- Do not scrape private sessions or account pages without explicit authorization.
- Do not bypass CAPTCHAs, paywalls, login walls, robots/terms restrictions, or rate limits.
- Confirm before browser actions that send, buy, delete, change accounts, submit private data, or otherwise have consequences.
- Do not use `browser_eval` in this research workflow unless a separate skill/request explicitly calls for gated eval and it is enabled.
