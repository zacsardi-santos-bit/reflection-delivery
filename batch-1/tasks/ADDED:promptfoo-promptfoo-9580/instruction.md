I've set up promptfoo to point at our on-premises deployment with a custom host URL, but some features are ignoring that configuration entirely.

*   CloudConfig.setApiHost() must strip trailing slashes from the provided host string before persisting it; for example, 'https://onprem.example.com/' must be stored as 'https://onprem.example.com'.

*   CloudConfig.getApiHost() must strip all trailing slashes from the host value, whether it comes from the config file (e.g., 'https://onprem.example.com///' → 'https://onprem.example.com') or from the PROMPTFOO_CLOUD_API_URL environment variable (e.g., 'https://env-host.example.com/' → 'https://env-host.example.com'). Internal path segments must be preserved; only the trailing slash is removed (e.g., 'https://onprem.example.com/prefix/' → 'https://onprem.example.com/prefix').

*   checkEmailStatus() must use cloudConfig.getApiHost() as the base URL for the HTTP request (constructing '${host}/api/users/status') when cloudConfig.isEnabled() returns true, and must NOT fall back to 'api.promptfoo.app'.

*   checkEmailStatus() must use the PROMPTFOO_CLOUD_API_URL environment variable as the base URL when cloudConfig.isEnabled() returns false, and must NOT call cloudConfig.getApiHost() in that case. Any trailing slash in the PROMPTFOO_CLOUD_API_URL value must be stripped so the resulting path does not contain '//api/users/status'.

*   guardrails.guard(), guardrails.pii(), and guardrails.harm() must route requests to '${cloudConfig.getApiHost()}/v1/{guard|pii|harm}' with an 'Authorization: Bearer ${cloudConfig.getApiKey()}' header when cloudConfig.isEnabled() returns true.

*   guardrails.adaptive() must route requests to '${cloudConfig.getApiHost()}/v1/adaptive' with an 'Authorization: Bearer ${cloudConfig.getApiKey()}' header when cloudConfig.isEnabled() returns true.

*   When cloudConfig.isEnabled() returns false, guardrails functions must route to 'https://api.promptfoo.app/v1/{endpoint}' and must NOT include an Authorization header. cloudConfig.getApiHost() must NOT be called on this code path.

*   When the PROMPTFOO_REMOTE_API_BASE_URL environment variable is set, guardrails functions must use that URL as the base (e.g., '${PROMPTFOO_REMOTE_API_BASE_URL}/v1/guard') even if cloudConfig.isEnabled() returns true. No Authorization header must be sent when using this override.

*   Guardrails functions must strip trailing slashes from the configured host URL before constructing the request URL (e.g., 'https://onprem.example.com/' must yield 'https://onprem.example.com/v1/guard', not 'https://onprem.example.com//v1/guard').

*   The POST /api/providers/http-generator route must send requests to '${cloudConfig.getApiHost()}/api/v1/http-provider-generator' with an 'Authorization: Bearer ${cloudConfig.getApiKey()}' header when cloudConfig.isEnabled() returns true. Trailing slashes in the configured host must be stripped.

*   The POST /api/providers/http-generator route must send requests to 'https://api.promptfoo.app/api/v1/http-provider-generator' with no Authorization header when cloudConfig.isEnabled() returns false.


*   Interface details: Type: Class
Name: CloudConfig
Location: src/globalConfig/cloud.ts
Description: Manages cloud/on-prem configuration. The exported singleton `cloudConfig` is used across the codebase.
Signature:
  setApiHost(host: string): void — persists the API host, stripping any trailing slashes before writing
  getApiHost(): string — returns the API host URL, stripping any trailing slashes from both the config-file value and the PROMPTFOO_CLOUD_API_URL env var; preserves path segments but removes only the trailing slash
  isEnabled(): boolean — returns whether the cloud/on-prem config is active
  getApiKey(): string | undefined — returns the API key for authenticating with the configured host

Type: Export
Name: cloudConfig
Location: src/globalConfig/cloud.ts
Description: Singleton instance of CloudConfig exported for use by accounts, guardrails, and server routes.

Type: Function
Name: checkEmailStatus
Location: src/globalConfig/accounts.ts
Description: Checks the email verification status by making an HTTP request. When cloudConfig.isEnabled() is true, uses cloudConfig.getApiHost() as the base URL (forming ${host}/api/users/status). When cloudConfig.isEnabled() is false, uses the PROMPTFOO_CLOUD_API_URL environment variable as the base URL and does NOT call cloudConfig.getApiHost(); trailing slashes in the env var URL are stripped.

Type: Object
Name: guardrails (default export)
Location: src/guardrails.ts
Description: Provides guardrail functions. When cloudConfig.isEnabled() is true, all functions route to the configured cloud host with a Bearer token. When disabled, falls back to the public endpoint without auth. When PROMPTFOO_REMOTE_API_BASE_URL is set, that URL wins regardless of cloud state, and no auth token is sent.
Signature:
  guard(input: string): Promise<...> — routes to /v1/guard
  pii(input: string): Promise<...> — routes to /v1/pii
  harm(input: string): Promise<...> — routes to /v1/harm
  adaptive(request: AdaptiveRequest): Promise<...> — routes to /v1/adaptive

Type: Route
Name: POST /api/providers/http-generator
Location: src/server/routes/providers.ts
Description: HTTP route handler that generates an HTTP provider configuration. When cloudConfig.isEnabled() is true, sends the request to ${cloudConfig.getApiHost()}/api/v1/http-provider-generator with Authorization: Bearer ${cloudConfig.getApiKey()} header. When not enabled, sends to https://api.promptfoo.app/api/v1/http-provider-generator with no Authorization header. Trailing slashes in the configured host are stripped.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.