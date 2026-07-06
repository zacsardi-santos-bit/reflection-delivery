## Description

The tracing system currently offers no way to explicitly control the parent relationship of a newly created span. Every span is automatically parented to whatever span is currently executing in the thread context. This is limiting in two key ways:

1. If you need to create a span that is a root of its own trace tree — even while execution is nested inside another span — there is no way to do that. The new span always becomes a child of whatever is currently running.
2. If you want to explicitly link a new span as a child of a specific, previously created span (rather than the currently executing one), there is currently no mechanism to express that intent.

## Expected Behavior

- When creating a span, it should be possible to explicitly declare that it has no parent, making it a trace root regardless of the current execution context.
- When creating a span, it should be possible to explicitly designate an existing span as its parent, overriding whatever span happens to be currently executing.
- Spans created without any explicit parent declaration should continue to inherit their parent from the current execution context, as they do today.
- Subscribers that receive new-span notifications should be able to inspect whether the span has an explicit parent, is an explicit root, or is inheriting its parent from context.

## Why This Matters

Distributed tracing systems often need to model relationships that don't align with the lexical call stack — for example, correlating work across async boundaries or attaching spans to a logical trace root created earlier. Without explicit parent control, the tracing library cannot faithfully represent these relationships.
