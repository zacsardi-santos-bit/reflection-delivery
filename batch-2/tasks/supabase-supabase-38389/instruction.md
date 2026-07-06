Implement a reusable filter bar component in the shared UI package to enable structured query building against tabular data. Ensure the component combines a free-text search input with a structured filter builder, supports various input modes, and handles nested filter groups. Additionally, provide utility functions for state mutations and hooks for managing UI state and options caching.

Requirements:

*   Implement the `FilterBar` component with the following props:
    *   `filterProperties`: Array of `FilterProperty` objects.
    *   `filters`: A `FilterGroup` object.
    *   `onFilterChange`: Callback receiving an updated `FilterGroup`.
    *   `freeformText`: String for free-text search.
    *   `onFreeformTextChange`: Callback receiving a string.
    *   `supportsOperators`: Optional boolean, defaults to false.
*   Render a text input with placeholder 'Search or filter...'.
*   Ensure clicking/focusing the search input opens a popover listing property labels from `filterProperties`.
*   Selecting a property in the popover must:
    *   Call `onFilterChange` with a new `FilterGroup` including a condition for the selected property.
    *   Reveal a value input for that property with an accessible label 'Value for {PropertyLabel}'.
*   Handle value input modes:
    *   Display options in a popover for properties with an array of strings.
    *   Render custom picker components directly in the popover for properties with a component object.
*   Close any open popover when clicking outside the `FilterBar`.
*   Display existing conditions from the `filters` prop immediately on render.
*   Render conditions from nested `FilterGroup` objects.
*   Hide logical operator labels by default unless `supportsOperators` is true.

Utility Functions:

*   Implement `findGroupByPath`, `findConditionByPath`, `addFilterToGroup`, `addGroupToGroup`, `removeFromGroup`, `updateNestedValue`, `updateNestedOperator`, and `updateNestedLogicalOperator` to handle filter tree manipulations.
*   Implement type guards: `isCustomOptionObject`, `isFilterOptionObject`, `isAsyncOptionsFunction`, and `isSyncOptionsFunction`.

Hooks:

*   Implement `useFilterBarState` to manage UI state with initial values and setters for `isLoading`, `error`, `selectedCommandIndex`, `isCommandMenuVisible`, `activeInput`, `isDialogOpen`, and a `resetState` function.
*   Implement `useOptionsCache` to manage options caching with `loadingOptions`, `propertyOptionsCache`, and `optionsError`. Ensure `loadPropertyOptions` is a no-op for properties with plain array options.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.