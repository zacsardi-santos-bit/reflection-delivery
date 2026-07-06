Implement a centralized AI client for making JSON-structured model requests in the Gemini CLI. Ensure it applies consistent defaults, retries on transient failures, strips markdown from responses, and handles errors consistently. Update the file edit utility to use this client, support async context tracking, and cache results.

*   Implement the `BaseLlmClient` class in `packages/core/src/core/baseLlmClient.ts`:
    *   Constructor must accept `ContentGenerator` and `Config` instances.
    *   Implement `generateJson(options: GenerateJsonOptions): Promise<object>` to:
        *   Use a retry mechanism with defaults: `temperature: 0`, `topP: 1`, `responseMimeType: 'application/json'`.
        *   Merge config overrides if provided, allowing fields like `topK`.
        *   Include `systemInstruction` only if explicitly provided.
        *   Strip markdown code fences from responses and log telemetry with `logMalformedJsonResponse`.
        *   Trim whitespace around JSON responses without logging telemetry.
        *   Throw specific errors for empty or malformed responses and call `reportError`.
        *   Propagate abort errors without reporting.
        
*   Update `Config` class in `packages/core/src/config/config.ts`:
    *   Implement `getBaseLlmClient()` method:
        *   Throw error if called before `refreshAuth()`: 'BaseLlmClient not initialized. Ensure authentication has occurred and ContentGenerator is ready.'
        *   Return a `BaseLlmClient` instance after `refreshAuth()`.

*   Update the utility in `packages/core/src/utils/llm-edit-fixer.ts`:
    *   Implement `FixLLMEditWithInstruction(instruction, old_string, new_string, error, current_content, baseLlmClient, abortSignal): Promise<SearchReplaceEdit>`:
        *   Use `promptIdContext` to get `promptId`, log warning and use fallback if not available.
        *   Construct user prompt with XML tags and pass to `generateJson`.
        *   Cache results by input parameters and bypass cache for differing parameters.
    *   Export `resetLlmEditFixerCaches_TEST_ONLY()` to clear caches.

*   Export `promptIdContext` from `packages/core/src/utils/promptIdContext.ts`:
    *   Provide `.run(value, callback)` to execute with a specific prompt ID.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.