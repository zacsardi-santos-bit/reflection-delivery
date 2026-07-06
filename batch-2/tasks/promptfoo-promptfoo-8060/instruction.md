I'm working on updating the OpenCode SDK provider in promptfoo to support the latest version of the SDK.

*   The OpenCodeSDKProvider must detect which SDK API version is available at runtime by attempting to import the v2 module; if the v2 module is unavailable (import throws), it must fall back to the v1 nested request shape.

*   When using v2 API shape, session.create must be called with a flat object containing title, and optionally directory, workspace, and permission at the top level (no body/query wrapping).

*   When using v2 API shape, session.prompt must be called with a flat object containing sessionID, parts, and optionally model, tools, format, variant, directory, and workspace at the top level (no path/body wrapping).

*   When using v2 API shape, session.delete must be called with { sessionID: string }.

*   When falling back to v1 API shape, session.create must use { body: { title }, query: { directory } }, session.prompt must use { path: { id, sessionID }, query: { directory }, body: { parts, permission? } }, and session.delete must use { path: { id, sessionID }, query: { directory } }.

*   After each callApi invocation where persist_sessions is not true, the provider must call session.delete to clean up the session. If deletion fails, the error must be silently caught and logged at debug level with a message containing 'Failed to delete non-persistent session <sessionID>'. The API result must still be returned normally.

*   The cleanup() method must delete all sessions that were created with persist_sessions enabled, using session.delete({ sessionID: string }) for each tracked session.

*   Session persistence (persist_sessions: true) must work without requiring a cache to be enabled — multiple callApi calls must reuse the same session without any external cache dependency.

*   When config.format.type is 'json_schema' and the SDK response includes a 'structured' field in the response info, the output must be the compact JSON stringification of that structured value.

*   When config.format.type is 'json_schema' and no 'structured' field is present in the response info, the provider must extract JSON from a fenced code block in the text parts and output it as a compact JSON string.

*   When config.workspace is set, the workspace value must be passed as a top-level 'workspace' field in both session.create and session.prompt calls. If workspace is configured but neither working_dir nor baseUrl is provided, the provider must throw an error with the message 'OpenCode SDK workspace support requires either baseUrl or working_dir'.

*   When config.format is set, it must be passed as a top-level 'format' field to session.prompt. When config.variant is set, it must be passed as a top-level 'variant' field to session.prompt.

*   When config.apiKey is set and config.provider_id is set, the apiKey must be injected into the createOpencode call as config.provider[provider_id].options.apiKey.

*   The env values passed to the provider constructor must be forwarded to the createOpencode call as the 'env' field.

*   The permission configuration must be included in the session.create call (not session.prompt) when using the v2 API shape.


*   Interface details: Type: Class
Name: OpenCodeSDKProvider
Location: src/providers/opencode-sdk.ts
Description: Provider that communicates with the OpenCode SDK. Supports both v2 (flat parameter shape) and v1 (nested parameter shape) API formats, automatic version detection, session lifecycle management, and structured JSON output.
Signature:
  callApi(prompt: string, context?: CallApiContextParams, options?: CallApiOptionsParams) -> Promise<ProviderResponse>
  cleanup() -> Promise<void>

Key behaviors required by the tests:

1. v2 SDK auto-detection:
   - On each callApi invocation, attempt to import `@opencode-ai/sdk/v2` via the ESM resolver.
   - If the import succeeds, use the v2 flattened parameter shape for session.create, session.prompt, and session.delete.
   - If the import fails (module not found), fall back to v1 nested parameter shape.

2. v2 session.create shape (flattened):
   - Called with a single flat object: { title: string, directory?: string, workspace?: string, permission?: object }
   - The `title` must match /^promptfoo-\d+$/.
   - `permission` config is passed here (not in session.prompt) in the v2 shape.

3. v2 session.prompt shape (flattened):
   - Called with a single flat object: { sessionID: string, parts: Array<{type: string, text?: string}>, model?: { providerID: string, modelID: string }, tools?: object, format?: object, variant?: string, directory?: string, workspace?: string }

4. v2 session.delete shape:
   - When no working_dir or workspace is configured: called with exactly { sessionID: string } — no additional fields.
   - When working_dir or workspace is configured: called with { sessionID: string, directory?: string, workspace?: string } — the directory/workspace values are spread from the session query.

5. v1 (fallback) session.create shape (nested):
   - Called with { body: { title: string }, query: { directory: string } }

6. v1 (fallback) session.prompt shape (nested):
   - Called with { path: { id: string, sessionID: string }, query: { directory: string }, body: { parts: Array<...>, permission?: object } }
   - IMPORTANT: The path object must contain BOTH `id` AND `sessionID` set to the same session ID value.

7. v1 (fallback) session.delete shape (nested):
   - Called with { path: { id: string, sessionID: string }, query: { directory: string } }
   - IMPORTANT: The path object must contain BOTH `id` AND `sessionID` set to the same session ID value.

8. Non-persistent session cleanup:
   - After each callApi (when persist_sessions is NOT true), call session.delete with the v2 or v1 shape.
   - If deletion fails, catch the error and log it at debug level with a message containing: "Failed to delete non-persistent session <sessionID>"
   - The callApi result must still be returned normally even when deletion fails.

9. Persistent session cleanup via cleanup():
   - When persist_sessions is true, track sessions created across callApi invocations.
   - When cleanup() is called, delete each tracked session using session.delete({ sessionID: string }) (v2 shape, exactly these fields with no extras when no working_dir/workspace was configured).

10. persist_sessions without cache:
    - Session persistence (reusing the same session across multiple callApi calls) must work without requiring a cache to be enabled.

11. JSON schema structured output:
    - When config.format.type === 'json_schema' and the SDK response info contains a `structured` field, output must be the compact JSON string (JSON.stringify) of the structured value.
    - When config.format.type === 'json_schema' and there is no `structured` field, extract the JSON from a fenced code block (```json ... ```) in the text parts and output a compact JSON string.

12. workspace config:
    - When config.workspace is set, pass it as a top-level `workspace` field to both session.create and session.prompt.
    - If config.workspace is set but neither config.working_dir nor config.baseUrl is provided, throw: "OpenCode SDK workspace support requires either baseUrl or working_dir"

13. format and variant config:
    - When config.format is set, pass it as a top-level `format` field to session.prompt.
    - When config.variant is set, pass it as a top-level `variant` field to session.prompt.

14. apiKey injection:
    - When config.apiKey is set and config.provider_id is set, include it in the createOpencode call as: config.provider[provider_id].options.apiKey

15. env overrides:
    - Pass the env property (from constructor options) to createOpencode as the `env` field.

16. session_id resume:
    - When context includes a session_id, call session.prompt with sessionID: <that session_id> (v2 flat shape) instead of creating a new session.

17. working_dir (directory):
    - In v2 shape: pass as top-level `directory` field to both session.create and session.prompt.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.