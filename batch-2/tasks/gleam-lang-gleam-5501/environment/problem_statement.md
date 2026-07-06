## Description

The Gleam compiler's error and warning messages include a "Hint" section at the end that offers developers actionable advice on how to resolve the issue. Currently, the hint text is not consistently formatted across different kinds of messages. In some messages the hint appears immediately after the message body with no blank line between them, making it visually blend into the body and harder to distinguish at a glance. In other messages a blank line is already present. This inconsistency makes the output harder to read.

Additionally, some hint messages about arithmetic operator mismatches use inconsistent capitalization — the hint sentence begins with a lowercase word — which looks unprofessional compared to other diagnostic messages. And in some cases, long hint strings are not wrapped to the standard display width, causing them to run longer than the rest of the output.

## Expected Behavior

- When a diagnostic message has body text followed by a hint, there should always be a blank line separating the two, making the hint clearly distinct from the explanation.
- Hint text that begins with a sentence fragment should follow standard sentence capitalization.
- Hint text should be wrapped to the same standard line width used by the rest of the compiler diagnostic output.

## Why This Matters

Consistent and readable error messages are a core part of the Gleam developer experience. Developers rely on hints to quickly understand how to fix compilation errors. Making the formatting uniform across all messages improves readability and gives the output a more polished, professional appearance.
