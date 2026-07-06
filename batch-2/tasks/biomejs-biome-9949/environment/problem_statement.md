## Description

Embedded frames that lack sandbox restrictions can expose applications to serious security vulnerabilities — untrusted content loaded inside them may execute scripts, access the parent page, or navigate the user without any protection. There's currently no automated way for the linter to warn developers about missing or insufficient sandbox restrictions on embedded frames in either plain HTML files or component-based template files.

## Expected Behavior

- When an embedded frame element has no sandbox restriction attribute at all, the linter should report an error explaining that the attribute is missing.
- When an embedded frame element in a component template has a sandbox attribute present but set only as a plain boolean flag (no string value specifying the allowed capabilities), the linter should also report an error, because a boolean-only value does not meaningfully restrict the frame's content in that context.
- Embedded frame elements that have a valid sandbox string value — including an empty string, which applies the most restrictive mode — should not be flagged.
- Embedded frame elements that receive their props via a spread should not be flagged, since the sandbox attribute may be provided dynamically.
- Other (non-frame) elements should not be affected by the rule.
- The rule should exist in the nursery group, as it is a new and evolving check.

## Why This Matters

Without an automated lint rule for this, developers may inadvertently embed third-party or user-generated content in frames without any restrictions, creating cross-site scripting vectors and privilege escalation risks. A linter rule makes it easy to catch these issues at development time before they reach production.
