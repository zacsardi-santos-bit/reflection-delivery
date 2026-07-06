Enhance the Sentry integration for schema validation errors to improve error message clarity and ensure all validation issues are accessible. Implement changes to indicate array elements in error paths and attach a complete list of issues when the limit is exceeded.

*   Update the `applyZodErrorsToEvent` function:
    *   Modify the signature to `applyZodErrorsToEvent(limit: number, includeAttachment: boolean, event: Event, eventHint: EventHint) -> Event`.
    *   Ensure that when `includeAttachment` is false and the exception is a `ZodError`, populate `event.extra` with up to `limit` flattened issues under the key `zoderror.issues`.
    *   Ensure no entries are added to `eventHint.attachments` when `includeAttachment` is false.
    *   When `includeAttachment` is true and the exception is a `ZodError`, add an attachment to `eventHint.attachments` with the filename `zod_issues.json`, containing a JSON-serialized object with all issues.
    *   Ensure `event.extra['zoderror.issues']` contains only up to `limit` issues, even when `includeAttachment` is true.

*   Implement the `flattenIssuePath` function:
    *   Convert a path array to a dot-separated string, replacing numeric segments with `<array>`.
    *   Return an empty string for an empty array.

*   Implement the `flattenIssue` function:
    *   Convert a `ZodIssue` to a flattened object with a dot-joined path string and JSON-serialized keys.
    *   Set `unionErrors` to undefined and preserve other fields.

*   Implement the `formatIssueMessage` function:
    *   Return a message formatted as 'Failed to validate keys: {paths}' for non-empty paths, using `flattenIssuePath`.
    *   Return 'Failed to validate {expected}' for issues with empty paths.

*   Export the following functions from `packages/core/src/integrations/zoderrors.ts`:
    *   `flattenIssue`
    *   `flattenIssuePath`
    *   `formatIssueMessage`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.