---
name: api-probe
description: Discover and characterize APIs behind websites or docs, including OpenAPI/Swagger specs, endpoints, auth methods, client-library choices, and MCP-tool design notes.
allowed-tools: market_list_available, market_read_package, market_install_package, mcp_list_servers, config_dir_list, config_dir_read, skills_list, history_recent, logs_recent
---

# API Probe

Use this skill when the user asks you to investigate a website, service, SaaS product, public documentation site, app, or repo to determine whether it exposes an API and how that API can be used.

This skill gives investigation guidance only. It does not grant browser, web search, shell, file, HTTP, credential, or coding powers by itself. Use only tools currently available to you. If web, browser, code, or network tools are missing, explain which MCP tool category is needed before claiming you can inspect a site.

## Objectives

When probing an API, produce a practical integration brief:

- API availability and confidence level.
- Official docs and source URLs inspected.
- API style: REST, GraphQL, gRPC, JSON-RPC, SOAP, WebSocket, SSE, webhook, or SDK-only.
- Base URLs and environments.
- Authentication methods.
- Important endpoints, operations, resources, scopes, and rate limits.
- Request/response examples.
- Pagination, filtering, sorting, idempotency, retries, errors, and versioning.
- SDK/client libraries worth using.
- MCP server design recommendations.
- Unknowns, risks, and follow-up tests.

## Probe Order

Prefer official, documented surfaces first:

1. Official API docs.
2. OpenAPI/Swagger/Rapidoc/Redoc specs.
3. SDK repositories and package metadata.
4. Public examples, Postman collections, and CLI docs.
5. Browser network inspection of the user's authorized session, only when the user explicitly asks and browser tooling is installed.
6. Static website assets as a last resort.

Do not scrape private sessions, guess credentials, bypass access controls, or probe aggressively.

## Common API Discovery Paths

When browser or HTTP tooling is available, check common paths:

```text
/openapi.json
/openapi.yaml
/swagger.json
/swagger.yaml
/api-docs
/api/docs
/docs/api
/docs/openapi.json
/swagger
/swagger-ui
/swagger/index.html
/redoc
/redocly
/.well-known/
/.well-known/openapi
/graphql
/graphiql
/playground
/api/graphql
/v1
/v2
/api/v1
/api/v2
```

Also inspect HTML for:

- `swagger-ui`
- `redoc`
- `rapidoc`
- `openapi`
- `graphql`
- `apiBaseUrl`
- `baseURL`
- `x-api-key`
- generated SDK links
- Postman collection links

## Browser/Website Investigation

If browser tooling is installed and the user asks you to inspect a live site:

- Open the official docs first.
- Use page source/search for `openapi`, `swagger`, `graphql`, `api`, `token`, `oauth`, and `apikey`.
- Inspect loaded JavaScript bundle names only when necessary.
- Watch network requests while navigating docs or app flows.
- Prefer documented API calls over private app-internal calls.
- Record endpoint paths and methods, but avoid copying secrets, cookies, bearer tokens, or personally sensitive data into output.

If a private app appears to use undocumented internal APIs, report that clearly and recommend building an MCP integration only if the user owns/controls the account and accepts breakage risk.

## Auth Detection

Identify auth methods from docs, headers, examples, or specs:

- API key in header, query, or basic auth.
- Bearer token.
- OAuth 2.0 authorization code, client credentials, device code, PKCE.
- JWT.
- Session cookie.
- HMAC signatures.
- Webhook signing secrets.
- mTLS or certificate-based auth.
- No auth/public endpoints.

Capture:

- token endpoint;
- authorization endpoint;
- scopes;
- header names;
- required env/config names;
- refresh behavior;
- sample safe config fields for future MCP server.

Never request, expose, or store real secrets unless the user explicitly provides them for a tool that is designed to use them.

## Spec And SDK Libraries

When designing tooling, choose libraries by API style and target language.

For Go MCP servers:

- Use `net/http` for simple REST integrations.
- Use `encoding/json` for JSON request/response models.
- Use `context.Context` on all network calls.
- Use `oauth2` libraries only when the flow is truly needed; keep API-key clients stdlib-only when possible.
- Use generated OpenAPI clients only if the spec is large and stable.
- Keep provider clients small and hand-written when only a few endpoints are needed.
- Use `tinychain/mcp` for MCP transport and tool schemas.

Useful discovery/parser libraries and tools when available:

- OpenAPI/Swagger: `kin-openapi`, `swaggest/openapi-go`, `openapi-generator`, `swagger-codegen`.
- GraphQL: introspection query, `gqlgen` models, or small hand-written HTTP POST client.
- HTML/docs extraction: browser tooling, Readability-style extraction, or parser MCP tools.
- JavaScript app inspection: browser network panel, source maps when publicly shipped, static bundle string search.
- Postman collections: parse JSON collection and extract endpoints/auth.
- HAR files: parse request flows when user exports them intentionally.

Do not add heavy generated clients unless the user wants broad coverage. For NullBot MCP tools, minimal hand-written clients are preferred.

## OpenAPI Workflow

When an OpenAPI spec is found:

1. Record spec URL and version.
2. Extract servers/base URLs.
3. Extract security schemes and scopes.
4. List tags/resources.
5. Select the smallest useful endpoint subset for the user's goal.
6. Identify required params, request bodies, and response schemas.
7. Note pagination/rate limit/error conventions.
8. Recommend MCP tools with names and schemas.

Example MCP mapping:

```text
GET /customers/{id}        -> get_customer
GET /customers             -> list_customers
POST /customers/search     -> search_customers
POST /tickets              -> create_ticket
GET /tickets/{id}/comments -> list_ticket_comments
```

## GraphQL Workflow

When GraphQL is detected:

- Check whether introspection is enabled.
- Identify endpoint URL.
- Identify auth requirements.
- Discover key queries/mutations/subscriptions.
- Prefer narrowly scoped MCP tools over one generic `graphql_query` tool unless the user explicitly wants raw query access.
- If raw query access is provided, document permissions and risks.

Recommended MCP mapping:

```text
graphql_introspect_schema
graphql_query_named
graphql_mutation_named
```

For product-specific MCP servers, prefer:

```text
list_projects
get_project
search_issues
create_issue
update_issue_status
```

## Webhooks And Events

If the service supports webhooks:

- list event types;
- record signature verification method;
- identify retry behavior;
- capture payload examples;
- note whether a local HTTP receiver or remote tunnel is needed.

Webhook receivers are usually better as a separate MCP/server mode, not as a simple stdio-only tool.

## Output Format

Return an API probe brief like:

```markdown
## API Probe: <service>

Confidence: high|medium|low
API style: REST|GraphQL|...
Docs inspected:
- <url>

Base URLs:
- production: <url>

Auth:
- method: OAuth2 authorization code
- scopes: ...

Key resources:
- users
- projects
- tickets

Useful endpoints:
| Method | Path | Purpose | Auth |
| --- | --- | --- | --- |

SDK/library notes:
- Go: stdlib net/http is enough for the initial MCP.

MCP design:
- tool_name: description, inputs, output shape

Risks:
- rate limits unknown
- internal API only

Next steps:
- fetch OpenAPI spec
- test auth with user-provided credentials
```

## Turning Findings Into MCP Tools

After discovery, propose a minimal MCP server:

- exact tool names;
- required config/env values;
- permissions;
- request/response models;
- error handling strategy;
- rate limit/retry strategy;
- release/package metadata.

If the `mcp-skill` is installed, use it together with this skill to create the server structure.

## Safety And Honesty

- Never claim an API exists unless you found documentation, specs, SDK code, or observed requests.
- Clearly label inferred private/internal APIs.
- Do not attempt authentication flows without user approval.
- Do not store credentials in skills.
- Do not exfiltrate cookies/tokens from browser sessions.
- If tools are missing, say what tool category is needed rather than pretending.
