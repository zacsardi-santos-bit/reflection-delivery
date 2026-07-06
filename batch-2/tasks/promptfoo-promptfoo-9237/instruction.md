I'm working on the xAI provider integration and need to update it to support some new models and fix a few issues that have come up.

*   The GROK_REASONING_EFFORT_MODELS constant must include 'grok-4.3' and 'grok-4.3-latest' in its list of models that support reasoning effort configuration.

*   When createXAIProvider is called with 'xai:grok-4.3', the resulting provider's supportsReasoningEffort() method must return true.

*   When getOpenAiBody is called for a grok-4.3 provider with reasoning_effort, presence_penalty, frequency_penalty, and stop parameters configured, the returned body must preserve reasoning_effort but strip presence_penalty, frequency_penalty, and stop (they must be undefined in the body).

*   calculateXAICost must return approximately 3.75 per 1,000,000 input and 1,000,000 output tokens for the following legacy/redirected model slugs: 'grok-4-1-fast-reasoning', 'grok-4-fast-non-reasoning', 'grok-4', 'grok-code-fast', 'grok-code-fast-1-0825', 'grok-3', 'grok-3-beta', 'grok-3-fast', 'grok-3-fast-beta', 'grok-3-fast-latest'.

*   createXAIImageProvider must accept 'xai:image:grok-imagine-image-quality', 'xai:image:grok-imagine-image-quality-latest', and 'xai:image:grok-imagine-image-quality-20260403' as valid model identifiers and return their own slug as the provider id.

*   When XAIImageProvider is instantiated with 'grok-imagine-image-quality-latest', callApi must route the request to the API using the canonical model slug 'grok-imagine-image-quality', and must return a cost of 0.05.

*   When XAIImageProvider is instantiated with an unknown 'grok-imagine-image-*' slug (one not in the known list), callApi must preserve that slug as-is rather than falling back to any default model.

*   When XAIImageProvider is instantiated with 'grok-imagine-image-quality' and a resolution config of '2k', callApi must send the resolution parameter to the generations endpoint and return a cost of 0.07.

*   When XAIImageProvider is instantiated with 'grok-imagine-image-quality' and an images config (for editing), callApi must route to the edits endpoint with the images included, and return a cost of 0.07.

*   When XAIImageProvider is instantiated with 'grok-imagine-image-pro' and an image config (for editing), callApi must return a cost of approximately 0.06.

*   When XAIImageProvider is instantiated with 'grok-imagine-image-pro' (generation, no source image), callApi must send 'grok-imagine-image-pro' as the model slug and return a cost of 0.05.

*   When XAIResponsesProvider streams a response containing response.reasoning_summary_text.delta events alongside response.output_text.delta events, the result.output must contain only the output_text content and must not include reasoning summary text or a 'Reasoning:' prefix.

*   When XAIResponsesProvider streams a completed response containing encrypted reasoning content (encrypted_content), the result.output must not expose the encrypted content, but result.raw.output must include an item with type 'reasoning' and the encrypted_content field preserved.

*   When XAIResponsesProvider encounters a malformed (non-JSON) delta event during streaming but receives a valid response.completed event, it must use the completed response's text as the final output rather than the truncated delta-assembled text.

*   When XAIResponsesProvider receives a streamed response.completed event with annotations on output text content, result.metadata.annotations and result.raw.annotations must both contain those annotations.

*   When XAIResponsesProvider receives a streamed response containing non-message output items such as web_search_call in the completed response, result.output must include both the web search call information (containing 'Web Search Call') and the final answer text.

*   When XAIResponsesProvider parses token usage from a streamed response.completed event, result.tokenUsage must include total, prompt, completion (mapped from output_tokens), and numRequests set to 1.


*   Interface details: Type: Constant
Name: GROK_REASONING_EFFORT_MODELS
Location: src/providers/xai/chat.ts (or similar xAI chat provider file)
Description: Array/list of model name strings that support the reasoning_effort parameter. Must include 'grok-4.3' and 'grok-4.3-latest'.

Type: Function
Name: createXAIProvider
Location: src/providers/xai/chat.ts (or similar xAI provider factory file)
Signature: createXAIProvider(modelId: string, options?: object) -> provider object
Description: Factory function that creates an xAI chat provider. The returned provider must have a supportsReasoningEffort() method and a getOpenAiBody(prompt, context) method.

Type: Method
Name: supportsReasoningEffort
Location: xAI chat provider class (returned by createXAIProvider)
Signature: supportsReasoningEffort() -> boolean
Description: Returns true if the model supports reasoning_effort, false otherwise. Must return true for 'grok-4.3' and false for 'grok-4-0709'.

Type: Method
Name: getOpenAiBody
Location: xAI chat provider class (returned by createXAIProvider)
Signature: getOpenAiBody(prompt: string, context: object) -> Promise<{ body: object }>
Description: Builds the request body for the OpenAI-compatible API call. For grok-4.3, must preserve reasoning_effort and strip presence_penalty, frequency_penalty, and stop parameters.

Type: Function
Name: calculateXAICost
Location: src/providers/xai/chat.ts (or similar cost calculation utility)
Signature: calculateXAICost(modelName: string, config: object, inputTokens: number, outputTokens: number) -> number | undefined
Description: Calculates the cost of an xAI API call. Must return approximately 3.75 for 1,000,000 input and 1,000,000 output tokens for legacy/redirected slugs: 'grok-4-1-fast-reasoning', 'grok-4-fast-non-reasoning', 'grok-4', 'grok-code-fast', 'grok-code-fast-1-0825', 'grok-3', 'grok-3-beta', 'grok-3-fast', 'grok-3-fast-beta', 'grok-3-fast-latest'.

Type: Function
Name: createXAIImageProvider
Location: src/providers/xai/image.ts (or similar xAI image provider factory)
Signature: createXAIImageProvider(modelId: string, options?: object) -> provider object
Description: Factory function that creates an xAI image provider. Must accept 'xai:image:grok-imagine-image-quality', 'xai:image:grok-imagine-image-quality-latest', and 'xai:image:grok-imagine-image-quality-20260403' as valid identifiers and return a provider whose id() method returns the full model string.

Type: Class
Name: XAIImageProvider
Location: src/providers/xai/image.ts (or similar xAI image provider file)
Description: Provider class for xAI image generation and editing. Constructor takes (modelName: string, options: { config: object }).
Methods:
  - callApi(prompt: string) -> Promise<{ cost?: number, output?: string, raw?: object, metadata?: object }>
  Behavior:
  - 'grok-imagine-image-quality-latest' routes to canonical slug 'grok-imagine-image-quality' in the API call; cost 0.05
  - Unknown 'grok-imagine-image-*' slugs are sent as-is to the API (no fallback redirect)
  - 'grok-imagine-image-quality' with resolution: '2k' → sends to generations endpoint with resolution param; cost 0.07
  - 'grok-imagine-image-quality' with images config → sends to edits endpoint; cost 0.07
  - 'grok-imagine-image-pro' with image config (edit) → cost approximately 0.06
  - 'grok-imagine-image-pro' without source image (generation) → preserves slug 'grok-imagine-image-pro' in API request; cost 0.05

Type: Class
Name: XAIResponsesProvider
Location: src/providers/xai/responses.ts (or similar xAI responses provider file)
Description: Provider class for the xAI responses streaming API. Constructor takes (modelName: string, options: { config: object }).
Methods:
  - callApi(prompt: string) -> Promise<{ output: string, tokenUsage?: object, raw?: object, metadata?: object }>
  Streaming behavior:
  - response.reasoning_summary_text.delta events are ignored when assembling output text
  - result.output contains only text from response.output_text.delta events, never prefixed with 'Reasoning:'
  - When a response.completed event is received, its output array is used for: token usage, annotations, non-message items, and encrypted reasoning content
  - Encrypted reasoning (encrypted_content from reasoning output items) is preserved in result.raw.output as items with type 'reasoning' and encrypted_content fields; does NOT appear in result.output
  - Annotations from output_text content in completed response are surfaced in result.metadata.annotations and result.raw.annotations
  - Non-message output items (e.g., web_search_call) from the completed response are rendered and appear in result.output (output should contain 'Web Search Call')
  - Malformed (non-JSON parseable) delta events are skipped; if a response.completed event was received, its text is used as result.output instead of the truncated delta-assembled text
  - result.tokenUsage is populated from completed event usage: { total: total_tokens, prompt: input_tokens, completion: output_tokens, numRequests: 1 }


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.