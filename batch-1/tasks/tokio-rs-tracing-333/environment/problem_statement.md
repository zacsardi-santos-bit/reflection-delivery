## Description

There is a bug in the tracing subscriber's filter directive ordering logic. When two filter directives happen to have the same structural "weight" — for example, when their target strings are the same length, or when they both specify the same number of fields — one of the directives silently overrides the other. This means that only one of the two filters is actually evaluated, and events or spans that should match the second directive are incorrectly dropped.

## Expected Behavior

- When two directives target different span names of equal length (e.g., both three characters), events from both targets should pass through.
- When two directives target the same span name but filter on different fields, both directives should match their respective spans.
- When two event directives each filter on a single (but different) field, both events should pass through.
- When two span-plus-field directives have the same-length span names and the same number of fields, both spans should be created and observed.

## Why This Matters

Users configuring multiple filter directives of similar specificity cannot rely on all of them being applied. This leads to confusing behavior where some events or spans are silently dropped, making the filter system unreliable for real-world use cases where multiple subsystems have similarly-named targets or fields.
