## Description

Our findings dashboard and grouped-findings view both send sort parameters to the backend API, but the sort token strings for the two API families are fundamentally different. Plain findings are sorted by database declaration order, so an ascending sort naturally puts the most critical and failing results first. Grouped findings, on the other hand, use numeric-weighted computed columns, meaning you need a descending sort to achieve the same visual ordering. Because these tokens were defined ad-hoc in various places without clear labelling, the wrong tokens occasionally got mixed between the two families — sending ascending sort tokens to the grouped API (or vice versa) causes either wrong ordering or a backend error about an invalid sort parameter.

Additionally, several filter utility functions — including helpers for detecting whether muted findings are included and for parsing multi-value comma-separated filter strings — lived inside modules that also imported server-only authentication code. This made it impossible to import them directly in the test environment, causing a cascade of test failures whenever those modules were mocked.

## Expected Behavior

- All sort tokens and preset composite sort strings should live in a dedicated, server-free module that can be safely imported anywhere.
- The module should clearly separate "Family A" (plain findings) and "Family B" (grouped findings) tokens so callers never accidentally mix them.
- Filter utility helpers (CSV parsing, muted-findings detection, combined failing-and-non-muted filter application) should similarly be extractable without pulling in server-only code.
- The findings page and the resource-drawer component should both use the shared default-muted-filter helper so that muted findings are hidden by default unless the user explicitly opts in.

## Why This Matters

Mixing sort token families silently produces wrong results or backend errors that are hard to diagnose. Keeping sort and filter utilities in server-entangled modules breaks test isolation and makes it impossible to write reliable unit tests for the action layer.
