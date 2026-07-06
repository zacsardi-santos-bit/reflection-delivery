## Description

The CLI assistant currently provides no useful guidance when users ask how to install a companion CLI tool. Users who ask install or migration questions receive generic fallback help rather than actionable, platform-specific installation instructions. Additionally, the startup banner for content related to this companion tool does not include any installation hints, missing an opportunity to guide new users immediately.

There is also an internal model routing gap: when users authenticate through Google accounts (Google login or Application Default Credentials), model name requests are sent directly to the backend as-is, even though the backend expects different internal model identifiers. This causes a mismatch between the model the user requests and the one actually routed. This mapping should be applied transparently only for those authentication paths, not for direct API or Vertex AI access.

A related model name constant was also recorded incorrectly and needs to be corrected to the proper model identifier.

## Expected Behavior

- When users ask how to install or migrate to the companion CLI tool, the assistant should detect the user's operating system and respond with the correct installation command for that platform (macOS/Linux, Windows PowerShell, or Windows Command Prompt).
- On unsupported platforms, a link to the documentation should be shown instead.
- If the query mentions the companion tool but does not ask about installation or migration, the assistant should fall back to its normal help output.
- The startup banner for companion tool content should append a platform-appropriate installation command inline for supported platforms.
- A built-in support guide for the companion tool should be available with a link to its getting-started documentation.
- Model name translation should be applied transparently when using Google account authentication, so that user-facing model names are mapped to the correct backend identifiers before being sent to the API.
- The model name translation must not be applied for Vertex AI, direct Gemini API, or Gateway authentication paths.

## Why This Matters

Users discovering the companion tool through the assistant's help system or startup banner need immediate, actionable installation guidance. Without platform-specific instructions, users are left to search documentation on their own. The model mapping fix ensures that requests using Google account authentication are correctly routed to the intended backend models without any user intervention.
