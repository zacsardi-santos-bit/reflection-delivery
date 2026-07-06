## Description

The Fireworks AI chat integration needs to be updated to work with the latest major version of the Fireworks SDK. The existing integration was written for the 0.x SDK and is now incompatible with the 1.x SDK that ships with a different error hierarchy, different import paths, and a different naming convention for the async API call method.

## Expected Behavior

- Error classes should be importable from the top-level Fireworks package. Two error classes have been renamed: the "invalid request" error is now called a "bad request" error, and the "service unavailable" error is now called an "internal server" error.
- The async client's call method should be named consistently with the sync client and must be a proper async function, not a regular function that returns an awaitable.
- Streaming usage tracking options must be forwarded to the API nested inside a special "extra body" container field. When streaming usage is disabled, no extra body field should be added at all. If a user accidentally provides duplicate stream options (both as a top-level setting and inside the extra body container), the extra-body version should win and a warning should be logged.
- The SDK's own built-in retry mechanism should be disabled during client construction, so that retries are not executed twice — once by the SDK and once by the integration layer.
- Legacy tuple-style timeout values should be automatically converted to the object format required by the new SDK.
- When a "prompt too long" error is raised, it should be promoted to a context-overflow error type that preserves the original HTTP response metadata (status code) and body.

## Why This Matters

Without this update, users of the Fireworks AI integration will see import errors and test failures after upgrading to the latest Fireworks SDK. The changes ensure backwards compatibility for existing user code (such as legacy timeout formats) while adopting the new SDK's conventions.
