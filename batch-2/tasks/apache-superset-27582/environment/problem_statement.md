## Description

Superset currently has no way to reference a named dataset metric from within a custom SQL expression using a Jinja template. If you want to reuse a metric's formula in a custom SQL expression, you have to copy and paste the metric's underlying SQL manually. This is tedious and error-prone — if the metric definition changes later, every place it was copied needs to be updated.

## Expected Behavior

- A new Jinja macro should be available that accepts a metric name (and optionally a dataset ID) and expands to that metric's underlying SQL expression at query time.
- When the dataset ID is not provided explicitly, the system should attempt to resolve it automatically from the current request context (e.g., from the current chart or datasource).
- If the macro is used with a dataset ID, the macro should look up only the specified dataset and should not require any additional context.
- If the dataset cannot be found, a clear error should be raised identifying which dataset ID was not found.
- If the metric name doesn't exist in the specified dataset, a clear error should be raised identifying both the metric name and the dataset.
- If no dataset ID can be determined (neither explicitly provided nor resolvable from context), a clear error should be raised asking the user to specify the dataset ID in the macro call.

## Why This Matters

This makes metric definitions reusable across custom SQL expressions without duplication. Analysts can reference a metric by name rather than copying its SQL, reducing maintenance burden and ensuring consistency when metric definitions change.
