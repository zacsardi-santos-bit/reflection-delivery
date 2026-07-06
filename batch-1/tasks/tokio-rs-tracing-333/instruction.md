Fix the bug in the tracing subscriber's filter directive ordering logic to ensure all configured directives are applied independently, even when they have similar structural properties. Ensure that no directive silently overrides another due to equal specificity.

*   Implement logic to handle multiple directives targeting spans with the same name but different fields.
    *   Ensure both directives independently match and allow the respective spans through.
*   Implement logic to handle multiple directives whose targets have the same string length.
    *   Ensure events from both targets pass through the filter.
*   Implement logic to handle multiple event field directives that match the same number of fields.
    *   Ensure events with either field pass through the filter.
*   Implement logic to handle multiple span-plus-field directives with the same-length span names and the same number of fields.
    *   Ensure both spans are created and observed at the correct level.
*   Develop a mechanism to produce a valid total ordering for directives with equal specificity.
    *   Ensure each directive is consulted independently and no directive silently overrides another.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.