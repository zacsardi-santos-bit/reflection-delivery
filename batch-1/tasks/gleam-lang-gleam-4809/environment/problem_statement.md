## Description

When writing pattern-matching code in Gleam, it's common to end up with a nested case expression where the outer case matches a value to extract a variable, and then immediately hands that variable off to an inner case expression. This double nesting is functionally correct but unnecessarily verbose and harder to read. The two levels could be expressed as a single flat case expression with combined patterns instead.

There is currently no automated way to flatten these nested case expressions. A language server code action that can detect this pattern and collapse the two nested case expressions into one would be very helpful.

## Expected Behavior

The refactoring should:
- Detect when a case clause's body is a case expression matching on a variable bound in the outer pattern
- Offer an action to collapse the two into a single flat case expression
- Correctly handle blocks wrapping the inner case expression
- Preserve all bound variables from the outer pattern that are not being collapsed
- Preserve labeled field names in constructor patterns
- Handle alternative patterns in inner clauses by distributing them across the outer pattern
- Add variable aliases when the matched variable is still used in an inner clause's body
- Correctly propagate and combine guard expressions from both levels, including correct parenthesization when guards contain logical-or operators

## Why This Matters

Reducing unnecessary nesting makes code easier to read and reason about. This is a common refactoring need that can be tedious to do by hand, especially when guards or labeled fields are involved.
