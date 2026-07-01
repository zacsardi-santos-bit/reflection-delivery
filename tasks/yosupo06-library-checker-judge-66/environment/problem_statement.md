# API Server: Fix Startup, Language List, and Submission Size Validation

## Description

The library checker judge's API server has several issues that make it non-functional in test environments and leave it open to abuse:

1. **Server crashes on startup** if a specific authentication secret is not configured as an environment variable. This makes it impossible to run integration tests without pre-configuring secrets.

2. **Language list returns empty** because the server looks for its language definitions file in a path that no longer exists after a recent directory restructuring. Callers asking for the list of supported languages get an empty response.

3. **No submission size limit** — the source code submission endpoint accepts arbitrarily large payloads without validation, which is a potential abuse vector.

## Expected Behavior

- The server should start without crashing even when the authentication secret is not explicitly set (fall back to a default value).
- The language list endpoint should return at least one supported language, loaded correctly from the file's new location.
- The submission endpoint should reject source code that exceeds 1 MiB (1,048,576 bytes), returning an error to the caller instead of accepting it.

## Why This Matters

These bugs block the addition of integration tests for the API server. Without fixing the startup issue and the broken language file path, the server cannot serve correct responses. Without size validation, the submission endpoint is unsafe to expose.
