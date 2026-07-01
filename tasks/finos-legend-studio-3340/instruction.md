Implement support for using model properties as right-hand side values in filter conditions within the query builder's filter panel. Enable users to drag properties or columns onto existing filter conditions, ensuring compatibility checks and providing clear error messages for unsupported actions.

*   Update the filter condition state:
    *   Replace the `value` property with `rightConditionValue`, an instance of `FilterValueSpecConditionValueState`.
    *   Access the underlying value through `rightConditionValue.value`.

*   Handle drag-and-drop functionality:
    *   Display a 'Change Filter Value' drop zone when a compatible property or non-derivation column is dragged onto an existing filter condition.
    *   Set the dragged property or column as the right-hand side value upon drop.
    *   Ensure the drop zone does not appear for incompatible types.

*   Implement reset functionality:
    *   Add a 'Reset' button on filter conditions with a right-hand side property value.
    *   Ensure clicking 'Reset' removes the property-based filter value.

*   Enforce restrictions with clear warnings:
    *   Reject dragging TDS derivation columns onto the filter panel or as right-hand side values, displaying appropriate warning messages.
    *   Reject dragging properties onto collection-based filter conditions or dragging collection-type properties onto non-collection filter conditions, with specific warning messages.

*   Ensure independent argument tracking for derived properties:
    *   Provide separate 'Set Derived Property Argument(s)...' buttons for each side of a filter condition.
    *   Ensure setting arguments on one side does not affect the other.

*   Handle operator changes:
    *   Convert an empty string value to an empty list when switching to a list-based operator.
    *   Display a validation issue and disable the Run Query button in this scenario.

*   Update filter tree behavior:
    *   Show the filter node's property name in the drop zone label when forming a logical group.

*   Ensure correct parsing and round-tripping:
    *   Parse and round-trip filter lambdas with property expressions correctly.

*   Provide test data:
    *   Include `TEST_DATA__lambda_filterWithRightSidePropertyExpression` in the test data file for filter lambdas comparing property expressions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.