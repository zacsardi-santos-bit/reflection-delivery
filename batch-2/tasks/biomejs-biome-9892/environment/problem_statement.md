## Description

Several lint rules in Biome produce diagnostic messages that are terse and unhelpful. The error messages tell developers what not to do but don't explain why the pattern is problematic or what they should do instead. This makes the diagnostics less useful, especially for developers who are unfamiliar with the specific pitfall being flagged.

## Affected Rules

The following rules have inadequate diagnostic messages:

- The rule detecting CSS shorthand properties that override earlier longhand declarations
- The rule detecting disallowed GraphQL root types
- The rule detecting misuse of the autofocus attribute in HTML
- The rule detecting self-comparisons in JavaScript/TypeScript
- The rule detecting objects, classes, and exports that expose a property named "then"

## Expected Behavior

Each of these rules should:

- Use a primary error message that **describes what was found** (factual, declarative), rather than an imperative command like "don't do X"
- Include additional note messages that:
  1. Explain **why** the flagged pattern is harmful
  2. Give **actionable guidance** on how to resolve the issue

For example, a rule about self-comparisons should not just say "this comparison is pointless" — it should explain that the same expression appears on both sides, explain that this is usually a mistake or sign that the wrong variable is being compared, and suggest concrete alternatives such as comparing two different values or using the appropriate NaN-checking utility if NaN detection is the goal.

## Why This Matters

Good diagnostic messages reduce friction when fixing lint violations. When the message explains the reason and suggests a fix, developers don't need to look up external documentation to understand what went wrong. Improving these messages makes the linter a better educational and productivity tool.
