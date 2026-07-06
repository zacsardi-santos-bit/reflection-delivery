## Description

Currently, when creating a new span, the only way to establish a parent-child relationship is through the ambient scope — the intended parent span must be actively attached to the current scope. This makes it impossible to create a child span under a parent that has already ended or is otherwise not active in the current context.

We need the ability to pass any existing span directly as an explicit parent when starting a new span, so developers can build custom span hierarchies without needing to manipulate scope state at all.

## Expected Behavior

- All three span creation methods should accept an optional parent span as part of their options.
- When an explicit parent is provided, the newly created span must record the parent's span ID as its parent span identifier, regardless of whether the parent is currently active on any scope.
- This should work for auto-finishing spans, inactive spans, and manually-finished spans alike.
- For manually-finished spans, the mechanism for ending the span should be calling the span's own end method directly. The separate finish-callback helper parameter should be removed.

## Why This Matters

This enables more flexible tracing scenarios: for example, associating work happening asynchronously or in a different context with a span that was started (and potentially ended) elsewhere, without needing to thread scope state through the application. It also simplifies the API for manual spans by removing an unnecessary indirection.
