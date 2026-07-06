## Description

The variable-aware variant of the if-then-else pipeline step cannot be used with relative date conditions that contain template variable placeholders. When building a pipeline step where a date range is expressed as a relative date (e.g., "1 year until <some variable date>"), the model validation fails immediately at parse time if the date field contains a variable placeholder string rather than an actual date value. This makes it impossible to define dynamic date range conditions using variables in if-then-else steps.

## Expected Behavior

- The variable-aware if-then-else step model should accept relative date conditions where the date portion of the relative date structure is a template variable placeholder string, without failing validation.
- Compound AND conditions combining multiple relative date conditions with variable placeholders should also be accepted without validation errors.
- The raw placeholder strings should be preserved on the parsed model so they can be resolved later.
- The step's variable resolution method should correctly substitute variable placeholders in the condition — both when the condition's value is a plain template string and when it is a relative date structure whose date portion contains the placeholder.

## Why This Matters

Users building pipelines with dynamic date ranges (e.g., filtering data for "the previous year" relative to a user-supplied date) rely on variable placeholders in relative date conditions. Without this fix, any if-then-else step that combines date-bound conditions with runtime variables is unusable.
