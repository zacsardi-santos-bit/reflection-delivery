Implement a new built-in function named `Zip` in the ferret query language's objects standard library. This function should combine two parallel arrays into an object, with the first array providing string keys and the second array providing corresponding values, similar to ArangoDB's functionality.

*   Ensure the `Zip` function accepts exactly two arguments.
    *   Return `(values.None, error)` if called with 0, 1, or more than 2 arguments.
*   Validate that both arguments are arrays.
    *   Return `(values.None, error)` if either argument is not an array.
*   Verify that the first argument (keys array) contains only string elements.
    *   Return `(values.None, error)` if any element in the keys array is not a string.
*   Check that both arrays have the same length.
    *   Return `(values.None, error)` if the lengths of the two arrays differ.
*   Construct and return an object mapping each key from the keys array to the corresponding value from the values array.
    *   Return `(*values.Object, nil)` on success.
*   Handle duplicate keys in the keys array by keeping only the first occurrence and its corresponding value.
    *   Ignore subsequent duplicate keys and their associated values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.