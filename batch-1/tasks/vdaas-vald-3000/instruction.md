Implement enhancements to the `JSONPathEval` function to expand its capabilities for JSON path evaluation. Ensure support for wildcard operations, numeric indexing, and aggregation functions to facilitate more dynamic and concise queries.

*   Implement the wildcard operator '*' in `JSONPathEval`:
    *   When applied to a map/object, collect all values and return them as a `[]any` slice.
    *   Ensure values are returned in alphabetical (sorted) key order for deterministic output.
    *   Support chaining with additional path segments to retrieve specific fields from each entry.
    *   Allow multiple consecutive wildcards to collect all values from sub-maps or sub-arrays, returning a flattened `[]any` slice.

*   Enhance array handling in `JSONPathEval`:
    *   Support numeric path segments for zero-based array index access, returning the element as a `float64` scalar.
    *   When the wildcard operator is applied to an array, iterate elements in their original order and flatten results into a single `[]any` slice.

*   Implement aggregation functions in `JSONPathEval`:
    *   Support a `sum()` function to aggregate all `float64` values in the current map or array, returning the total as a `float64`.
    *   Implement a `length()` function for maps to return the count of keys as an `int`.

*   Ensure nested `[]any` values produced by wildcard expressions are recursively flattened into a single flat `[]any` before returning.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.