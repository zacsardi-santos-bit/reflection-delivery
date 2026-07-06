## Description

Several lint rules in the nursery group currently emit diagnostic messages that tell developers what to prefer without clearly explaining what the flagged code is actually doing wrong. The messages jump straight to a recommendation ("Prefer X over Y") without first describing the problem with the current pattern, making it harder for developers to understand *why* their code is being flagged.

## Expected Behavior

The diagnostics for these rules should follow a clearer three-part structure:

- **Describe the problem**: Explain what the flagged expression is doing (e.g., "This expression uses ... and then checks whether the result is empty.")
- **Explain why the alternative is better**: Give a concrete reason for the suggested change, focused on intent and correctness
- **Provide a direct suggestion**: When no automatic fix is available, include a short, actionable note telling the developer what to use instead

The rule that detects unnecessary filter-then-emptiness-check patterns, the rule that detects filter-then-index-access patterns, and the rule that detects string method usage instead of the equivalent regex method should all be updated to follow this structure.

## Why This Matters

When the linter fires, developers benefit from understanding what their code is doing before being told what to do instead. Clear, structured diagnostic messages reduce confusion and help developers make informed decisions. Consistent message structure across related rules also improves the overall quality of the linter output.
