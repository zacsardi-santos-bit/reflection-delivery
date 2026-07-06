## Description

The OpenCode SDK provider needs to be updated to support the newer version of the underlying SDK's API. The SDK has introduced a v2 API that uses a flat parameter structure instead of the previous nested format. The provider currently always uses the legacy nested format, which will break once users upgrade to SDK versions that only expose the flat API.

## Expected Behavior

- The provider should automatically detect which API version is available and use the appropriate request shape.
- When the newer v2 module is available, all session operations (create, prompt, delete) should use the flat parameter format where fields are passed directly at the top level.
- When the v2 module is not available, the provider should fall back gracefully to the legacy nested format.
- Non-persistent sessions should be deleted after each call completes, with any deletion errors silently logged rather than surfacing to users.
- Persistent sessions should be cleaned up when the provider is shut down.
- Session persistence should not require a cache to be enabled.
- New configuration options should be supported: workspace association, output format with schema validation, response variant selection, API key injection into provider-specific config, and environment variable overrides for the spawned process.
- When structured JSON output is requested and the response includes a structured payload, that payload should be used directly rather than parsing the text output.
- When structured JSON output is requested and no structured payload exists, fenced JSON code blocks in the text response should be normalized to compact JSON.
- Using the workspace feature without also specifying a working directory or base URL should produce a clear error message.

## Why This Matters

As the OpenCode SDK evolves, the provider must track API changes to stay functional. The current hard-coded nested parameter format will cause failures with updated SDK versions. Additionally, proper session lifecycle management (automatic deletion of temporary sessions) avoids resource leaks on the server side.
