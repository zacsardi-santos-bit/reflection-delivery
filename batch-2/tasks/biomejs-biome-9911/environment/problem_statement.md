## Description

When developers migrate code from template literal–style string interpolation to JSX, they sometimes accidentally leave behind a dollar sign that served as part of the interpolation syntax. In JSX, curly braces alone are used to embed expressions — the dollar sign has no special meaning and will simply appear as visible text in the rendered output. This is usually an unintentional mistake that is easy to miss in code review.

There is currently no lint rule in the nursery group that detects this pattern.

## Expected Behavior

A new lint rule should:

- Flag any JSX text node that ends with a dollar sign when the next sibling is a JSX expression
- Report the issue as a warning with an automatic (unsafe) fix that removes the offending dollar sign
- Handle multiple occurrences within the same element individually, flagging each one separately
- Recognize the special case where a lone dollar sign is the only text before a single expression (such as displaying a price), and treat that as intentional — not a bug

## Why This Matters

This type of mistake causes confusing UI output where users see an unexpected dollar sign rendered on screen. Having a lint rule catch it automatically prevents the bug from reaching production and makes the migration from template literals to JSX safer and less error-prone.
