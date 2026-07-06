## Description

VS Code release notes are published to multiple audiences: Stable channel users, Insiders channel users, and web-only users. Currently the release notes markdown has no mechanism for authors to mark sections as relevant to only one of these audiences. As a result, all users see all content, including content that doesn't apply to their version of the product.

## Expected Behavior

- Release note authors should be able to wrap content in special conditional markers that declare which product context that content belongs to (e.g. Stable-only, Insiders-only, or in-product-only sections).
- When release notes are rendered, the renderer should detect the current user's context (channel/quality) and automatically show only the content blocks that apply to that context.
- Content that doesn't match the current context should be silently removed from the rendered output.
- Content outside any conditional block should always appear regardless of context.
- Condition matching when evaluating these blocks should be case-insensitive.
- Multiple conditional blocks within a single document should each be evaluated independently.

## Why This Matters

This allows authors to maintain a single release notes document that is automatically tailored to each audience at render time, eliminating the need to maintain separate documents per channel and preventing users from seeing irrelevant information.
