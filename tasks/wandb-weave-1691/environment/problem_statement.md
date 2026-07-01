## Description

When querying call traces, there is currently no way to filter results based on the actual values stored inside the dynamic input and output fields. Users can only filter by structural metadata (op names, call IDs, trace IDs, etc.), but cannot express conditions like "find all calls where the input value was greater than 5" or "find calls whose output contains a certain string."

## Expected Behavior

- The call query API should accept an optional expression-based filter that allows filtering calls by the content of their dynamic fields (inputs, outputs, attributes, summary).
- The expression language should support equality, greater-than, and greater-than-or-equal comparisons.
- Logical operators (AND, OR, NOT) should be composable with comparison operators.
- It should be possible to reference nested fields using dot notation, including array indices.
- Type conversions should be supported (e.g., treating a stored value as an integer or string) so comparisons across types can work. When a conversion fails for a given record, that record should be excluded rather than causing an error.
- Substring matching should be supported, with both case-sensitive and case-insensitive options.
- A separate summary endpoint should be available that returns only the count of matching calls, without fetching all call records. This is useful for pagination, counts, or aggregations.

## Why This Matters

Without this capability, users have to fetch all calls and filter them client-side, which is expensive and does not scale. With expression-based server-side filtering, users can efficiently narrow down the set of calls they care about and get accurate counts without transferring large amounts of data.
