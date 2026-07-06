## Description

Our internal JSON path evaluation library is too limited for the queries we need to run. It can navigate to specific named fields and count elements, but it has no way to iterate across all entries in an object at once, access array elements by position, or aggregate numeric values. This makes it impossible to write path expressions that collect a field from every sub-object, or that sum up values distributed across multiple keys.

## Expected Behavior

- A wildcard operator should be supported in path expressions so that all values in an object or array can be collected in one step, without needing to know the key names in advance.
- Chaining a wildcard with additional path segments should retrieve a specific field from each matching entry and return the collected values as an ordered list.
- Multiple consecutive wildcards should work and return a flat list of all matched values.
- Accessing an array element by its zero-based numeric position should be possible using a numeric path segment.
- A sum function should be available to aggregate all numeric values in the current object or array into a single total.
- When wildcard expressions produce nested collections, they should be automatically flattened into a single list.
- When wildcards fan out over map entries, results must be returned in a consistent, predictable order (alphabetical by key).

## Why This Matters

Without these capabilities, callers must manually iterate over all entries and aggregate results themselves, which defeats the purpose of having a path evaluation library. The missing wildcard and aggregation support prevents us from writing concise queries against structured data where the keys are dynamic or when summing distributed counts.
