## Description

The Sentry integration for schema validation errors has two shortcomings when reporting validation failures.

First, when a validation error occurs inside an array element, the error message shown in Sentry doesn't indicate that the failure happened within an array. For example, if a field named "names" contains an invalid entry at index 1, the error message currently shows only the field name without any indication that the failure is inside an array element. This makes it hard to understand the exact location of the failure.

Second, when there are many validation issues, only a limited number are included in the event extras. Any issues beyond the limit are silently dropped, so developers looking at a Sentry event may not see the full picture of what went wrong during validation.

## Expected Behavior

- Error messages should clearly represent array access in the path, using a standardized placeholder text to indicate that an array element was involved.
- When there are more validation issues than the configured limit, all issues should still be accessible — not just the first few. The complete list should be attached as a separate file so no issues are lost.
- The limit should continue to apply to the inline extras (keeping events lightweight), while the full list is available via the attachment.

## Why This Matters

Validation errors in production can be complex, involving many fields and deeply nested structures. If developers can only see a partial list, or if the path is ambiguous about whether an array was involved, it slows down debugging. Both the clearer path format and the attachment fallback improve the developer experience when diagnosing validation failures.
