Optimize the Big Number with Trendline chart plugin by reducing unnecessary server queries and centralizing aggregation logic. Implement client-side computation for most aggregation methods and update the SQL viewer's format toggle for clarity.

*   Update the `buildQuery` function:
    *   Return exactly 2 queries when `formData.aggregation` equals 'raw'.
        *   Ensure the second query (queries[1]) has `post_processing: []`, `is_timeseries: false`, and `columns: []`.
    *   Return exactly 1 query for other aggregation values ('sum', 'LAST_VALUE', 'mean', 'min', 'max', 'median').
        *   Include `post_processing` with operations: { operation: 'pivot' }, { operation: 'rolling' }, { operation: 'resample' }, { operation: 'flatten' }.

*   Define and export `aggregationChoices` constant in `@superset-ui/chart-controls`:
    *   Include keys: 'raw', 'LAST_VALUE', 'sum', 'mean', 'min', 'max', 'median'.
    *   Each entry must have a `label` string and a `compute` function: `(data: number[]) => number | null`.
    *   Ensure keys are lowercase and declared as `const`.

*   Modify the `transformProps` function:
    *   Import `aggregationChoices` from `@superset-ui/chart-controls`.
    *   Use `aggregationChoices` for client-side computation of the headline number for all methods except 'raw'.
    *   Ensure aggregation keys are lowercase (e.g., 'sum').
    *   Set `bigNumberFallback` to null when `bigNumber` is computed as a non-null value.

*   Update the `ViewQuery` component:
    *   Ensure the SQL format toggle switch displays 'formatted' when checked and 'original' when unchecked.
    *   Achieve an accessible switch name of 'formatted original'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.