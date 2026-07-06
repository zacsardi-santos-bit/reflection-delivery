Extend Pinot's audit logging system to include response auditing. Implement a mechanism to log HTTP response details alongside incoming requests, ensuring both share a unique identifier for correlation. Enable this feature through a configuration flag, and ensure it operates only when the global audit system is active.

*   Update `AuditLogFilter`:
    *   Accept `Provider<Request>`, `AuditRequestProcessor`, and `AuditConfigManager` in the constructor.
    *   Implement `ContainerRequestFilter` and `ContainerResponseFilter` interfaces.
    *   In the request filter:
        *   If both `AuditConfig.isEnabled()` and `AuditConfig.isCaptureResponseEnabled()` are true, create an `AuditResponseContext` with a UUID `requestId` and current timestamp (`System.nanoTime()`). Store it in `ContainerRequestContext` with the key `"audit.response.context"`.
        *   Ensure `requestId` matches the pattern `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}`.
        *   If either flag is false, do not set `"audit.response.context"`.
    *   In the response filter:
        *   If either `AuditConfig.isEnabled()` or `AuditConfig.isCaptureResponseEnabled()` is false, do not perform auditing.
        *   Retrieve `AuditResponseContext` from `"audit.response.context"`. If null or invalid, return without logging or throwing exceptions.
        *   With a valid context, log an `AuditEvent` via `AuditLogger.auditLog()` with `requestId`, `responseCode`, `durationMs`, `endpoint`, and `method`.
        *   Ensure `requestId` is consistent between request and response events.
        *   Call `AuditLogger.auditLog()` twice per request/response cycle when enabled.
        *   Calculate `durationMs` as `(System.nanoTime() - startTimeNanos) / 1,000,000`.
        *   Handle exceptions gracefully without affecting the HTTP response or propagating errors.

*   Implement `AuditResponseContext`:
    *   Provide a no-arg constructor.
    *   Include fluent setters: `setRequestId(String)` and `setStartTimeNanos(long)`.
    *   Ensure `startTimeNanos` is set with a positive value from `System.nanoTime()`.

*   Update `AuditConfig`:
    *   Add `captureResponseEnabled` boolean (default false).
    *   Provide `setCaptureResponseEnabled(boolean)` and `isCaptureResponseEnabled()` methods.

*   Update `AuditEvent`:
    *   Add fields: `requestId` (String), `responseCode` (Integer), and `durationMs` (Long).
    *   Provide fluent getters and setters for these fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.