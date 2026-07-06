Implement a fix in the `partial_model.rs` file to ensure that SQL queries generated for partial models with active enum fields and table aliases include the necessary type casts. Ensure that the enum-to-text cast is consistently applied in all scenarios involving table aliases, both at the top-level and within nested partial models.

*   Update the derive macro implementation in `partial_model.rs` to handle struct-level aliases correctly:
    *   When a struct-level alias is used, ensure the generated SQL includes a CAST expression for enum fields, qualifying the column with the alias and casting it to 'text'.
    *   For example, with alias 'zzz' and a field mapped from column 'tea', the SQL should be: `SELECT CAST("zzz"."tea" AS "text") AS "<field_name>"`.
*   Ensure explicit column mappings (from_col) in aliased structs use the field name as the output column alias:
    *   Apply the struct-level alias for table qualification and include the enum cast.
*   Handle nested partial models with their own aliases:
    *   Apply the struct-level alias to top-level enum fields and the nested alias to nested enum columns, both with casts.
    *   For example, with top-level alias 'aaa' and nested alias 'foo', generate: `SELECT CAST("aaa"."tea" AS "text") AS "tea", CAST("foo"."tea" AS "text") AS "nested_tea"`.
*   Ensure consistent application of the enum-to-text cast for all active enum fields in aliased scenarios, aligning with existing behavior for non-aliased and nested aliased scenarios.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.