Implement a feature to ensure that when a structured tracing event with an error value is converted to a Sentry breadcrumb, the error is stored under its original field name in the breadcrumb's data map. Ensure the error value is formatted correctly and the breadcrumb retains the correct level and message.

*   Preserve the original field name for error values in the breadcrumb's data map.
    *   Store the error under the exact field name used in the tracing event.
*   Format the error value as a JSON array of strings in the breadcrumb data.
    *   Each string should be formatted as 'ErrorType: error_message'.
    *   'ErrorType' should reflect the Rust type name of the error.
    *   'error_message' should be the display representation of the error.
*   Ensure the breadcrumb reflects the tracing event's severity and message.
    *   Set the breadcrumb level to Warning for warning-level tracing events.
    *   Set the breadcrumb message to the event's log message.
*   Maintain accessibility of the error chain by the original field name in the breadcrumb's data map.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.