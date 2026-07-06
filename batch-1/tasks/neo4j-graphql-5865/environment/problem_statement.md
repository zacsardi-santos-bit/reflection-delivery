## Description

The default value directive in Neo4j GraphQL schemas has incomplete support across the full range of scalar types it is supposed to work with. Several temporal scalar types — time-only values (both with and without timezone), local datetime values, and date-only values — cannot have default values declared in the schema, even though these are first-class supported scalar types in the library. In addition, the large integer type is not handled for default values.

Beyond the missing type support, the system is also overly strict about floating-point fields: it rejects integer literals as valid defaults for float-type fields and float list fields, even though integers are numerically compatible with floats. The same over-strict rejection applies to the coalesce directive on float fields and float list fields.

Finally, the error message shown when someone incorrectly applies the default value directive to unsupported types (such as spatial/geographic types) is outdated. It does not accurately list which types are actually supported, making the error unhelpful to developers trying to understand what they can use.

## Expected Behavior

- Declaring a default value on temporal scalar fields (time with and without timezone, local datetime, date-only) should be valid and work correctly at runtime — creating a node without supplying the field should populate it with the declared default.
- Declaring a default value on large integer fields should be valid, accepting both numeric and string representations, and return values as strings.
- Integer literals should be accepted as valid default and coalesce values for float-type fields.
- When the default value directive is used on an unsupported type (such as a spatial type), the error message should clearly list all supported types.

## Why This Matters

Developers who try to use default values on temporal or large integer fields currently get no error (silent failure) or incorrect rejections, making the schema behave unexpectedly. Developers using integer literals for float defaults get a confusing rejection. The fix makes the default value feature complete and predictable across all its declared supported types.
