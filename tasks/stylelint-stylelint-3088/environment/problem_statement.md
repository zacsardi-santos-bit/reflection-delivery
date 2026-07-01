## Description

It would be very useful to have lint rules that allow teams to restrict which CSS selector relationship types are permitted in their stylesheets. Currently, there is no way to enforce that certain selector relationships are always forbidden or that only certain ones are allowed.

## Expected Behavior

Two complementary new rules should be added:

- A **blacklist** rule: Given a list of forbidden selector relationship types, the rule should report a violation whenever one of those relationship types is used in a selector.
- A **whitelist** rule: Given a list of allowed selector relationship types, the rule should report a violation whenever a relationship type is used that is NOT in the allowed list.

Both rules should:
- Treat all whitespace-equivalent forms of the descendant relationship (space, tab, newline between selectors) as the same type for comparison purposes.
- Correctly handle combinators inside pseudo-class selectors such as negation selectors.
- Gracefully ignore non-standard CSS reference combinator syntax (a word wrapped between forward slashes, used in some CSS working drafts), rather than flagging them.

Additionally, a utility function should be introduced that determines whether a given combinator node from the selector parser represents standard CSS syntax (as opposed to a reference combinator).

## Why This Matters

Teams often want to enforce consistent use of selector relationships for performance, maintainability, or architectural reasons. For example, a project might want to forbid the descendant combinator entirely to prevent accidentally broad style application, or to only allow direct child or sibling selectors. Having dedicated lint rules for this makes such policies enforceable automatically.
