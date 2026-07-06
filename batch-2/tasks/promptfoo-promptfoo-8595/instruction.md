I'm working on the promptfoo evaluation framework and I need to add support for the Abliteration AI service as a new built-in provider.

*   The AbliterationProvider class must be exported from src/providers/abliteration.ts. When constructed with a model name string, provider.id() must return 'abliteration:<model_name>' and provider.toString() must return '[Abliteration Provider <model_name>]'.

*   AbliterationProvider defaults: config.apiBaseUrl must default to 'https://api.abliteration.ai/v1', config.apiKeyEnvar must default to 'ABLIT_KEY', and config.showThinking must default to false.

*   The API base URL resolution must follow this priority order (highest wins): (1) config.apiBaseUrl passed in constructor options, (2) providerOptions.env.ABLIT_API_BASE_URL, (3) process.env.ABLIT_API_BASE_URL, (4) the default 'https://api.abliteration.ai/v1'. Empty strings must be treated as unset and fall through to the next priority level.

*   The toJSON() method must return an object with shape { provider: 'abliteration', model: string, config: { apiBaseUrl: string, apiKey: undefined, apiKeyEnvar: string, showThinking: boolean } }. The apiKey field must always be undefined in JSON output regardless of whether it was set (it is redacted).

*   The getApiKey() method must resolve the API key in this priority order: (1) config.apiKey from constructor options, (2) env.ABLIT_KEY from providerOptions.env, (3) process.env.ABLIT_KEY. It must NOT fall back to OPENAI_API_KEY. The getOrganization() method must return undefined and must NOT use OPENAI_ORGANIZATION.

*   When callApi() is called without an API key available, it must throw an error with the message: 'API key is not set. Set the ABLIT_KEY environment variable or add `apiKey` to the provider config.'

*   When callApi() is called with a valid API key, it must send a POST request to '<apiBaseUrl>/chat/completions' with headers Authorization: 'Bearer <apiKey>' and Content-Type: 'application/json'. The request must NOT include an OpenAI-Organization header. The returned result must include output (string) and tokenUsage ({ total, prompt, completion, numRequests: 1 }).

*   When the API response includes reasoning_content and showThinking is false (the default), callApi() must return only the message content as output. When showThinking is true, the output must be formatted as 'Thinking: <reasoning_content>\n\n<final_answer>'.

*   When the API returns an HTTP error response (4xx or 5xx), callApi() must return { error: 'API error: <status> <statusText>', metadata: { http: { status: <number>, headers?: <object> } } } instead of throwing. Rate limit (429) responses must include the response headers in metadata.http.headers.

*   The createAbliterationProvider function must be exported from src/providers/abliteration.ts. It accepts a string in the format 'abliteration:<model_name>' or 'abliteration:chat:<model_name>' and returns an AbliterationProvider. The 'chat:' segment is treated as an alias and stripped from the resulting id. Model names may contain colons (e.g., 'abliteration:chat:org:model:name' produces id 'abliteration:org:model:name').

*   createAbliterationProvider must throw with message 'Abliteration provider requires a model name. Use format: abliteration:<model_name> or abliteration:chat:<model_name>' when the model name is empty or missing (e.g., inputs 'abliteration:' or 'abliteration:chat').

*   The loadApiProvider function in src/providers/index.ts must recognize the 'abliteration:' prefix and delegate to createAbliterationProvider, returning an AbliterationProvider instance with correct defaults. It must also pass through environment variable overrides (both context env and provider-level env) to the AbliterationProvider instance.

*   When loadApiProvider is called with 'abliteration:chat' or 'abliteration:' (missing model name), it must reject with the same error message as createAbliterationProvider throws.


*   Interface details: Type: Class
Name: AbliterationProvider
Location: src/providers/abliteration.ts
Description: Provider class for the Abliteration AI service. Handles authentication, API base URL resolution, request construction, and response parsing for Abliteration models.
Signature:
  constructor(modelName: string, options?: ProviderOptions)
  id(): string  — returns 'abliteration:<modelName>'
  toString(): string  — returns '[Abliteration Provider <modelName>]'
  toJSON(): { provider: 'abliteration', model: string, config: { apiBaseUrl: string, apiKey: undefined, apiKeyEnvar: string, showThinking: boolean } }
  getApiKey(): string | undefined
  getOrganization(): undefined
  callApi(prompt: string): Promise<ProviderResponse>
  config: { apiBaseUrl: string, apiKeyEnvar: string, showThinking: boolean, apiKey?: string }
  env?: Record<string, string | undefined>

Type: Function
Name: createAbliterationProvider
Location: src/providers/abliteration.ts
Signature: createAbliterationProvider(providerPath: string, providerOptions?: ProviderOptions): AbliterationProvider
Description: Factory function that parses an 'abliteration:<model_name>' or 'abliteration:chat:<model_name>' string and returns an AbliterationProvider instance. Throws if the model name is missing.

Integration note: The loadApiProvider function in src/providers/index.ts must be updated to recognize the 'abliteration:' prefix and delegate to createAbliterationProvider.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.