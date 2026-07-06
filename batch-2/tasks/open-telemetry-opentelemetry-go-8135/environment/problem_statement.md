## Description

There is currently no way to declare, at instrument creation time, which attribute keys should be retained in metric data points for a specific instrument. Developers who want to reduce metric cardinality for a particular instrument must rely on global view-level attribute filters, which apply broadly rather than at the individual instrument level. A per-instrument default attribute allowlist would give developers fine-grained control over which attribute dimensions are recorded for each instrument without requiring them to write and register views.

## Expected Behavior

- When creating any metric instrument (synchronous or asynchronous, integer or float, counter/gauge/histogram/up-down-counter), a developer should be able to specify which attribute keys are "allowed by default."
- Measurements recorded with additional attributes beyond the allowed set should produce data points containing only the allowed keys.
- Calling this option with no keys should result in all attributes being dropped (empty attribute set on data points).
- If a view explicitly matches the instrument and configures its own attribute settings, the view's configuration should take precedence over the per-instrument defaults.
- The feature should be available as an experimental option, separate from the stable metrics API.

## Why This Matters

High-cardinality attributes attached to metric instruments can overwhelm backends and increase cost. Developers often know at instrumentation time which attributes are meaningful, and should be able to express that intent directly when creating an instrument rather than relying on separate view configuration.
