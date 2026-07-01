## Description

The current telemetry system dispatches metrics to a background worker thread for delivery. This approach has become difficult to test, maintain, and integrate with the rest of the node infrastructure. We need to replace it with a class-based telemetry manager that buffers metrics internally and delivers them through the existing worker pool.

## Expected Behavior

- Telemetry can be enabled or disabled at construction time based on configuration.
- When disabled, submitting a metric should be a silent no-op — no state changes, no errors.
- Submitting a metric with no fields should be rejected with an error, since a metric without fields is invalid.
- Valid metrics should be buffered in an internal queue until the queue is flushed.
- When the queue reaches its maximum capacity, it should be flushed automatically.
- Flushing sends all queued metrics through the worker pool to the telemetry API.
- If the pool encounters an error during a flush and the queue is not saturated, the points should be retained for a retry attempt and the error should be logged.
- The new telemetry class should be the single place responsible for enabling/disabling collection, queuing metrics, and handling retries.

## Why This Matters

The previous implementation relied on a separate background task that was hard to test, coupled telemetry delivery directly to a worker thread, and didn't integrate cleanly with the rest of the node's infrastructure. The new approach improves testability, reliability (via retry logic), and consistency with how other subsystems are managed.
