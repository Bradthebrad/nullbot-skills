---
name: web-search
description: Search the web, triage results, fetch public URLs safely, and extract readable source text with NullBot Web MCP.
allowed-tools: web_workspace_info, list_search_providers, web_search, fetch_url
---

# Web Search

Use this skill when the user needs current public web information, source discovery, URL fetching, or readable text extraction through `nullbot-web-mcp`.

This skill gives guidance only. It does not grant web/network tools by itself.

## Start Here

1. Call `list_search_providers` or `web_workspace_info` to check provider availability.
2. If no supported key is present, explain that live `web_search` is unavailable and use provided URLs with `fetch_url` if available.
3. If a provider is available, use `web_search` with a focused query and a modest result limit.
4. Fetch promising sources with `fetch_url` before relying on snippets.

## Provider Behavior

- `web_search` uses provider selection from server config or request override.
- Brave is the implemented live search provider in v0.1 when `BRAVE_API_KEY` is present.
- Tavily, SerpAPI, and Bing may appear in status but are unsupported adapters in this build unless documentation says otherwise.
- Never print or infer API key values; only mention key presence/absence.

## Result Triage

When reviewing `web_search` results:

- Prefer official, primary, and recent sources.
- Use snippets only for triage, not final claims.
- Fetch the source URL and base final answers on page content whenever possible.
- Compare multiple independent sources for contentious, time-sensitive, or factual claims.
- Track rank, title, URL, provider, and snippet relevance.

## Fetching URLs

Use `fetch_url` for public `http`/`https` URLs. Expect it to reject:

- non-HTTP schemes;
- embedded credentials;
- localhost/private/link-local/multicast/unspecified targets;
- unsafe redirects;
- oversized responses beyond the configured cap.

Common `fetch_url` modes:

- `readability` for article-like clean text and lightweight markdown structure;
- `text` for plain extracted text;
- `headers` when checking metadata/status/content type;
- `raw` only when raw content is needed and size caps are acceptable.

## Readability Extraction

Readability is lightweight, not a full browser. It removes common noise such as scripts, styles, nav/header/footer/sidebar/banner/modal/social blocks and normalizes text. If a page relies on JavaScript or content is missing, switch to browser reading with the browser automation skill.

## Answering With Sources

Final responses should include:

- the URLs actually fetched/read;
- what each source supports;
- uncertainty or missing checks;
- note if live search was skipped because no provider key was present.

Avoid claiming a source says something based only on search snippets.
