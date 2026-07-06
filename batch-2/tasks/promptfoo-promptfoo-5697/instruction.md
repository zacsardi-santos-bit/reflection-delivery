Implement an "exists" operator for metadata filters in the evaluation results filtering system. This operator should allow users to filter results based on the presence of a metadata field with a meaningful value, without specifying the exact value.

*   Extend the metadata filter system to support the 'exists' operator:
    *   Ensure the 'exists' operator matches rows where the metadata field has a non-empty, non-whitespace string, any number (including 0), any boolean (including false), any array (including empty), or any object (including empty).
    *   Ensure the 'exists' operator does NOT match rows where the metadata field is absent, null, an empty string, or a whitespace-only string.
    *   Properly escape special characters in metadata field names during query construction to prevent misinterpretation.

*   Update the `queryTestIndices` method in `src/models/eval.ts`:
    *   Accept filter objects with the 'exists' operator in the filters array.
    *   Use the filter object shape: `{ logicOperator: 'and' | 'or', type: 'metadata', operator: 'exists', field: string, value: '' }`.
    *   Return an object with `filteredCount` (number) and `testIndices` (number[]).

*   Modify the `ResultsFilterOperator` type alias in `src/app/src/pages/eval/components/store.ts` to include 'exists'.

*   Implement logic in `isFilterApplied` (or equivalent) in `src/app/src/pages/eval/components/store.ts`:
    *   Count a metadata filter with 'exists' as applied if the field is present.
    *   Ensure consistency in `addFilter`, `removeFilter`, and `updateFilter` operations.

*   Update the `FiltersForm` component in `src/app/src/pages/eval/components/ResultsFilters/FiltersForm.tsx`:
    *   Display the 'Exists' operator in the dropdown only when the filter type is 'metadata'.
    *   Hide the value input when 'exists' is selected.
    *   Clear the filter value to an empty string when 'exists' is selected.
    *   Reset the operator to 'equals', clear the value, and field when changing the filter type away from 'metadata' while 'exists' is active.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.