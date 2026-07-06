# Add rate limiting for fatal engine error logs in the LLM serving layer

## Description

When a backend inference engine crashes and becomes unresponsive, every incoming request fails with the same fatal error. Currently, each of these failures logs a full traceback independently. In production with high request rates, this means hundreds or thousands of identical tracebacks flood the logs within a single cooldown period, making it extremely difficult to diagnose the root cause or understand the scope of the incident.

## Expected Behavior

- When a fatal engine error is first encountered, a full traceback should be logged for debugging purposes.
- Subsequent identical fatal errors within a configurable cooldown window should be silently suppressed rather than logged again.
- When the cooldown window expires, a brief summary should be emitted indicating how many errors were suppressed during the window.
- After a prolonged quiet period with no fatal errors, the system should reset so the next occurrence is treated as a fresh event and logs a full traceback again.
- Non-fatal errors (e.g. bad client requests or other application errors) should always be logged individually and should not be affected by this rate limiting.
- The error response returned to callers should still include the request ID in the error message.

## Why This Matters

Without this change, a single engine crash in a busy serving deployment causes a log explosion that can exhaust disk space, overwhelm log aggregation systems, and obscure the actual root cause. Operators need a way to see the first traceback clearly and then receive a summary of how many requests were impacted, without drowning in duplicate stack traces.
