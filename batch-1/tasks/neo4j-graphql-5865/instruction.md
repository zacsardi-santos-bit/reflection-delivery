Ensure that the Neo4j GraphQL schema supports default values for all specified scalar types, including temporal and large integer types. Update schema validation and runtime behavior to handle these defaults correctly, and improve error messaging for unsupported types.

*   Update schema validation to:
    *   Accept the @default directive on LocalDateTime fields with valid local datetime string values.
    *   Accept the @default directive on Time fields with valid time string values.
    *   Accept the @default directive on LocalTime fields with valid local time string values.
    *   Accept the @default directive on Date fields with valid date string values.
    *   Accept the @default directive on BigInt fields with either numeric integer or string values.
    *   Accept integer literals as valid @default values for Float and Float list fields.
    *   Accept integer literals as valid @coalesce values for Float and Float list fields.
    *   Throw an error with the message: '@default directive can only be used on fields of type Int, Float, String, Boolean, ID, BigInt, DateTime, Date, Time, LocalDateTime or LocalTime.' when @default is applied to spatial types, with the error path as ['TypeName', 'fieldName', '@default'].
    *   Throw an error with the message: '@default directive can only be used on fields of type Int, Float, String, Boolean, ID, BigInt, DateTime, Date, Time, LocalDateTime or LocalTime.' when @default is applied to unsupported non-scalar types.

*   Update runtime behavior to:
    *   Populate fields with the declared default value when creating a node without providing a String, Int, Float, or Boolean field with a @default directive. Ensure integer literals used as Float defaults produce float results.
    *   Populate BigInt fields with the default value and return it as a string in the GraphQL response.
    *   Populate DateTime fields with the specified ISO datetime string and return it in the same format.
    *   Populate LocalDateTime, Time, or LocalTime fields with the default value, omitting milliseconds in response values.
    *   Populate Date fields with the specified date string and return it in the same format.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.