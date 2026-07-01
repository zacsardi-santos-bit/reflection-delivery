Implement a built-in function named `Merge` in the `objects` package located at `pkg/stdlib/objects/merge.go`. This function should combine multiple objects into a single unified object, handling various input scenarios and edge cases as specified.

*   Implement the `Merge` function with the following signature:
    *   `Merge(ctx context.Context, args ...core.Value) (core.Value, error)`

*   Ensure the `Merge` function:
    *   Requires at least one argument. Return an error and `values.None` if called with no arguments.
    *   Validates that all arguments are objects or a single array of objects.
        *   Return an error and `values.None` if any argument is not an object.
    *   Returns a new object containing all properties from a single object argument.
    *   Combines properties from multiple object arguments into a new object.
        *   When the same key exists in multiple objects, the value from the last object should prevail.
    *   Produces a result independent of source objects by deep copying cloneable values.
    *   Merges all objects in a single array argument into one object.
        *   Return an empty object if the array is empty.
        *   Return an error and `values.None` if the array contains non-object elements.
    *   Returns an error and `values.None` if more than one array is passed as arguments.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.