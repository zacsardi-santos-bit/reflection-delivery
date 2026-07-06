Update the `planProgress` API view in the `AutomationAPI` class to return a response with correctly formatted timestamps and message lists. Ensure the JSON and XML outputs adhere to the specified formats, making the data easily consumable by clients.

Requirements:

*   Modify the `handleApiView` method in `AutomationAPI` to handle the "planProgress" API view.
    *   Ensure the returned `ApiResponse` has `getName()` equal to "planProgress".
*   JSON Output:
    *   Include 'planId' as an integer.
    *   Format 'started' and 'finished' as ISO 8601 UTC timestamp strings (e.g., '2024-04-08T18:13:20Z').
        *   Use `Date.toInstant().toString()` for formatting.
        *   If a date is null, represent it as an empty string.
    *   Serialize 'info', 'warn', and 'error' as flat JSON arrays of plain strings.
        *   Avoid nested objects or wrapped API response types.
*   XML Output:
    *   Serialize the response as a `<planProgress>` element with `type="set"`.
    *   Include 'planId', 'started', and 'finished' as simple text child elements.
    *   Format 'started' and 'finished' as ISO 8601 UTC strings, using the same method as for JSON.
    *   Serialize 'info', 'warn', and 'error' as `<list>` elements with `type="list"`, containing individual `<message>` child elements for each string.
    *   Ensure the element order is: `planId`, `started`, `finished`, followed by `info`, `warn`, and `error`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.