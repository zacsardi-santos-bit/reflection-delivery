I'm trying to evaluate AI agents built on n8n workflows using promptfoo.

*   The N8nProvider class must be exported from src/providers/n8n.ts and implement the ApiProvider interface. The constructor accepts a webhook URL string and an optional options object (with id and config fields). If neither the URL argument nor config.url is set, the constructor must throw an error with the message 'n8n provider requires a webhook URL'.

*   The id() method must return 'n8n:webhook:<12-char-lowercase-hex>' derived from an SHA-256 fingerprint of the URL and config, unless a custom id is provided that does not equal 'n8n' and does not start with 'n8n:'. If the provided id equals 'n8n' or starts with 'n8n:', it must be replaced with the fingerprint-based safe ID.

*   The toString() method must return '[n8n Provider ' + provider.id() + ']'.

*   callApi must call fetchWithCache with arguments (url, requestInit, timeout, 'text', true, maxRetries). The noCache argument (5th position) must always be true. For POST and PATCH methods, maxRetries (6th position) must be 0. For GET and PUT methods, maxRetries must be undefined. The HTTP method string must be normalized to uppercase before any comparison.

*   The default request body is { prompt: <the rendered prompt string> }, sent as JSON with Content-Type: application/json. For GET requests, body fields must be appended as URL query parameters and no request body must be sent.

*   Custom body templates (config.body as an object or JSON string) support Nunjucks interpolation. The {{prompt}} placeholder always resolves to the rendered prompt value, overriding any vars.prompt. When config.body is a JSON string, it must be JSON-parsed first before template rendering so that special characters (quotes, newlines) in the prompt are safely escaped in the resulting JSON.

*   Custom headers (config.headers) support Nunjucks template interpolation including {{env.VAR_NAME}} references. When config.sessionHeader is set and vars.sessionId is a non-empty string, that value must be sent both as the value of the configured header and as the 'sessionId' field in the request body. The explicit session value takes priority over any session ID returned from a previous response.

*   Abort signals provided via callOptions.abortSignal must be forwarded as the signal property of the fetch request init.

*   HTTP responses with a status code outside 200–299 must be returned as { error: 'n8n webhook call error: HTTP <status> <statusText>' }. Caught fetch exceptions must be returned as { error: 'n8n webhook call error: <error.message>' }.

*   Responses containing a truthy 'error' field must be returned as { error: 'n8n webhook response error: <errorValue>' }. This includes nested array formats such as [{ json: { error: 'message' } }]. Falsey error values (false, null, empty string, 0) must NOT trigger an error return.

*   Response output must be extracted from the following formats in order: { output }, { response }, { message: { content } }, array [{ json: { output } }] (reads first item's json.output and nested sessionId/actions), plain string (used as-is), null (returns empty string).

*   Tool calls must be extracted from tool_calls (array of { name, arguments }) or actions (array of { tool, input } mapped to { name: tool, arguments: input }). The metadata.toolCalls field must only be set when the extracted array is non-empty; empty arrays must not produce any metadata on the response.

*   Session IDs extracted from the response's sessionId field (or nested in array item json) must be returned as result.sessionId. They must NOT be automatically included in subsequent callApi calls.

*   When config.transformResponse is a string, it must be evaluated as a JavaScript expression with a 'json' variable for dot-path extraction (e.g. 'json.nested.deep.value'). When it is an async function, it must be awaited so the output is the resolved value rather than a Promise.

*   Debug log messages from callApi must use only the provider ID (n8n:webhook:...) to identify the request. They must never contain the webhook URL, URL credentials, query parameter values, prompt content, or response content.

*   The createN8nProvider function must be exported from src/providers/n8n.ts. It must strip the 'n8n:' prefix from the provider path to extract the webhook URL, fall back to options.config.url, and throw 'n8n provider requires a webhook URL' if no URL is available.

*   The createN8nProvider fingerprint must be stable across config key ordering (canonical sorted-key JSON serialization). Two providers with identical configs but different key insertion order must produce the same id(). Two providers with different body, headers, method, or transformResponse values must produce different ids.

*   Sensitive header names (matching a case-insensitive pattern covering Authorization, Cookie, Proxy-Authorization, X-Api-Key, X-Auth-Token, X-Access-Token, X-Secret, X-Signature, and Bearer) must have their values replaced with a redacted sentinel before fingerprinting, so rotating API credentials does not change the provider id. The presence vs absence of a sensitive header must still produce different ids. Non-sensitive header values (e.g. X-Workflow) must still differentiate ids.

*   The src/providers/registry.ts providerMap must include a factory entry whose test function returns true for paths equal to 'n8n' or starting with 'n8n:', and whose create function delegates to createN8nProvider.

*   The isFoundationModelProvider function in src/providers/constants.ts must return false for any provider ID starting with 'n8n'. The 'n8n' prefix must be added to the list of non-foundation-model provider prefixes.

*   The fetchWithRetries function's retry failure log messages and rate-limit log messages must sanitize the request URL before writing it to debug output: basic-auth credentials (username:password@) and known sensitive query parameters (e.g. token) must be stripped while the hostname must remain visible.

*   The monkeyPatchFetch function's connection error log message must sanitize both the target URL and the HTTP proxy URL (from HTTP_PROXY or HTTPS_PROXY environment variables) before writing to debug output: credentials and query tokens must be stripped while hostnames remain visible.


*   Interface details: Type: Class
Name: N8nProvider
Location: src/providers/n8n.ts
Description: Provider that calls n8n webhook endpoints, parses their response formats, and exposes tool calls and session IDs. Implements the ApiProvider interface.
Signature:
  constructor(webhookUrl: string, options?: ProviderOptions)
    - `webhookUrl`: The webhook URL string. May be empty if `options.config.url` is provided.
    - `options.id`: Optional custom provider ID. If it equals `'n8n'` or starts with `'n8n:'`, it is treated as a URL-backed routing ID and replaced with the fingerprint-based safe ID.
    - `options.config`: Optional N8nProviderConfig object.
    - Throws `Error('n8n provider requires a webhook URL')` if neither `webhookUrl` nor `options.config.url` is set.
  id(): string
    - Returns `n8n:webhook:<12-char-lowercase-hex>` (a SHA-256 fingerprint of URL + config), unless a custom non-URL-backed id was supplied to the constructor.
  toString(): string
    - Returns `[n8n Provider ${this.id()}]`
  callApi(prompt: string, context?: CallApiContextParams, callOptions?: CallApiOptionsParams): Promise<ProviderResponse>
    - Sends request to the webhook URL using fetchWithCache with signature: fetchWithCache(url, requestInit, timeout, 'text', true, maxRetries)
    - `noCache` argument (5th) is always `true`
    - `maxRetries` (6th): `0` for POST and PATCH; `undefined` for GET, PUT, and other methods
    - HTTP method string is normalized to uppercase before comparison
    - Default request body: `{ prompt: string }` (JSON-encoded, POST)
    - GET method: encodes body fields as URL query parameters; no request body
    - Custom body templates: `config.body` object or JSON string with Nunjucks `{{prompt}}` and `{{varName}}` substitution; `{{prompt}}` always resolves to the rendered prompt value regardless of vars.prompt
    - String body templates: JSON-parsed first before rendering so special characters in prompt are safely escaped
    - Custom headers: `config.headers` with Nunjucks template support including `{{env.VAR_NAME}}`
    - Session header: if `config.sessionHeader` is set and `vars.sessionId` is a non-empty string, that value is sent as the specified header AND added to the request body as `sessionId`
    - Session IDs from responses are NOT automatically forwarded to subsequent calls
    - Abort signals: forwarded from `callOptions.abortSignal` as `signal` in request init
    - Response parsing (applied before transformResponse):
      * HTTP status outside 200–299: returns `{ error: 'n8n webhook call error: HTTP {status} {statusText}' }`
      * Response data containing a truthy `error` field (including nested `[{json:{error}}]`): returns `{ error: 'n8n webhook response error: {errorValue}' }`; falsey values (false, null, '', 0) are NOT treated as errors
      * `{ output }` field → output
      * `{ response }` field → output
      * `{ message: { content } }` field → output
      * Array `[{ json: { output } }]` → output extracted from first item's json
      * Plain string → output as-is
      * null/empty → output is empty string
    - Tool calls: `tool_calls` array → `metadata.toolCalls` as `[{name, arguments}]`; `actions` array → `metadata.toolCalls` as `[{name: action.tool, arguments: action.input}]`; only attached to response if the array is non-empty
    - Session IDs extracted from `sessionId` field (or nested in array `[{json:{sessionId}}]`) → `result.sessionId`
    - `transformResponse` string: evaluated as a JavaScript expression with `json` variable (e.g. `'json.nested.deep.value'`)
    - `transformResponse` async function: properly awaited before returning
    - Fetch errors (thrown exceptions): return `{ error: 'n8n webhook call error: {error.message}' }`
    - Debug logs must use only the provider ID; must NOT log the webhook URL, credentials, query parameters, prompt content, or response content

Type: Interface
Name: N8nProviderConfig
Location: src/providers/n8n.ts
Description: Configuration shape for N8nProvider.
Fields:
  url?: string          - Webhook URL (alternative to constructor argument)
  method?: 'GET' | 'POST' | 'PUT' | 'PATCH'  - HTTP method (default: 'POST')
  headers?: Record<string, string>            - Additional request headers
  body?: Record<string, any> | string         - Request body template
  transformResponse?: string | ((json: any, text: string) => any)  - Response transformation
  timeout?: number      - Request timeout in milliseconds
  sessionHeader?: string  - Header name for sending session ID
  sessionParser?: string  - JS expression to extract session ID from response
  sessionField?: string   - Body field name for session ID (default: 'sessionId')

Type: Function
Name: createN8nProvider
Location: src/providers/n8n.ts
Signature: createN8nProvider(providerPath: string, options?: ProviderOptions): N8nProvider
Description: Factory function that creates an N8nProvider from a provider path string.
  - If `providerPath` starts with `'n8n:'`, strips the prefix to extract the webhook URL
  - Falls back to `options.config.url` if no URL in path
  - Throws `Error('n8n provider requires a webhook URL')` if no URL is available
  - Passes all options to the N8nProvider constructor
  - Provider fingerprint behavior:
    * Two providers with the same URL and config produce the same ID regardless of config key insertion order (canonicalized via sorted-key JSON)
    * Different URL, body, headers, method, or transformResponse values produce different IDs
    * Sensitive header values (matching Authorization, Cookie, Proxy-Authorization, X-Api-Key, X-Auth-Token, X-Access-Token, X-Secret, X-Signature, Bearer patterns — case-insensitive) are replaced with a redacted sentinel before fingerprinting, so rotating credentials does not change the provider ID
    * The presence vs absence of a sensitive header still produces different IDs
    * Non-sensitive header values (e.g., X-Workflow) still differentiate IDs

Type: Modification
Name: isFoundationModelProvider
Location: src/providers/constants.ts
Description: Existing function that must return false for any provider ID starting with 'n8n'. The internal NON_BASE_MODEL_PROVIDERS list must include 'n8n'.

Type: Modification
Name: providerMap
Location: src/providers/registry.ts
Description: The existing providerMap array must include a factory entry for n8n providers.
  - test function: returns true for paths equal to `'n8n'` OR starting with `'n8n:'`
  - create function: delegates to createN8nProvider(providerPath, providerOptions)
  - The same factory entry handles both `'n8n'` (URL in config) and `'n8n:https://...'` (URL in path) forms

Type: Modification
Name: fetchWithRetries (URL sanitization in retry logs)
Location: src/util/fetch/index.ts (or src/fetch.ts)
Description: Retry failure log messages and rate-limit log messages must use a sanitized form of the URL that strips basic-auth credentials (username:password) and known sensitive query parameters (e.g., token) before writing to debug logs. The hostname must remain visible.

Type: Modification
Name: monkeyPatchFetch (URL sanitization in connection error logs)
Location: src/util/fetch/monkeyPatchFetch.ts (or similar)
Description: Connection error log messages must use sanitized URLs for both the target URL and the HTTP proxy URL (HTTP_PROXY/HTTPS_PROXY env vars). Credentials (username, password) and query tokens must be stripped; hostnames must remain visible.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.