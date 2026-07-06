Implement support for array deconstruction in the one-line rightward assignment pattern matching syntax for the Natalie Ruby implementation. Ensure that arrays and objects supporting the array decomposition protocol can be matched against multiple named variables in a single assignment expression.

*   Support the one-line rightward assignment operator with multiple local variable targets separated by commas:
    *   For arrays on the left-hand side, assign each element to the corresponding variable.
    *   For non-array objects, call the object's `deconstruct` method and use its return value to bind the target variables.
*   Handle errors appropriately:
    *   Raise a `NoMatchingPatternError` with the message `'<inspected_value>: <inspected_value> does not respond to #deconstruct'` if the object does not implement the `deconstruct` method.
    *   Raise a `NoMatchingPatternError` with the message `'<inspected_value>: <inspected_value> length mismatch (given <actual_length>, expected <expected_length>)'` if the number of elements does not match the number of target variables.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.