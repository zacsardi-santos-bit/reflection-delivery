I'm working with a filter package that defines several filter types for different data types (strings, integers, floats, booleans, and time values). Each filter type holds optional conditions like equality checks, comparisons, pattern matching, and logical operators (AND/OR). Currently there's no way to check whether a filter is "empty" — meaning it has no conditions set at all.

I'd like each filter type to have a method that returns whether the filter has any conditions configured. A filter should be considered empty only when none of its fields have been set. As soon as any single field is set — even a boolean field set to false — the filter should be considered non-empty, since a false value is still a meaningful filter condition.

This would let callers quickly skip processing or query-building when the user hasn't actually specified any filter criteria.
