## Description

When a structured tracing event includes an error value attached to a named field and that event is captured as a Sentry breadcrumb, the error information is currently stored under a generic, fixed key in the breadcrumb's data map — regardless of what the developer actually named the field. This means the original field name is discarded, and if multiple error fields are present, they would all be merged together under that single key.

## Expected Behavior

- When a tracing event carries an error value in a named field, the resulting breadcrumb should store the error under that original field name in its data map.
- The value should be a list of human-readable strings, each describing an error in the chain with its type and message.
- The breadcrumb level and message should continue to reflect the tracing event's severity and log text.

## Why This Matters

Developers using structured logging rely on field names to identify what each piece of data represents. Losing the field name when converting to a breadcrumb makes it impossible to look up or distinguish specific errors by name. Preserving the original field name improves debuggability and makes breadcrumb data more useful when diagnosing issues in Sentry.
