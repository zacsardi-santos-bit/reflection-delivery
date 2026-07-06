Update the tortoise-orm codebase to be compatible with the new version of the SQL query-building library. Implement changes to ensure SQL generation methods accept the new SQL context object and update the retrieval of the database quote character. Ensure that all SQL expressions and database management operations function correctly with these updates.

*   Modify the database query class for each backend:
    *   Add a `SQL_CONTEXT` attribute with a `quote_char` property.
    *   Update `tortoise/contrib/test/__init__.py` in the `truncate_all_models` function to retrieve the quote character using `model._meta.db.query_class.SQL_CONTEXT.quote_char`.

*   Update SQL criterion objects:
    *   Ensure they accept a `DEFAULT_SQL_CONTEXT` argument from `pypika_tortoise.context` in their `get_sql()` method.
    *   The method `get_sql(DEFAULT_SQL_CONTEXT)` should return the correct SQL string.

*   Revise internal tortoise-orm SQL-generation methods:
    *   Update overrides of `get_sql`, `get_value_sql`, `get_special_params_sql`, `get_parameterized_sql`, and `get_arg_sql` to accept a `SqlContext` positional parameter instead of `**kwargs`.

*   Ensure Q filter criterion rendering produces correct SQL strings:
    *   Simple equality: `"id"=8`.
    *   AND combinations: `"id">8 AND "id"<10`, `"id"=8 AND "intnum"=80`.
    *   OR combinations: `"id">8 OR "id"<10`, `"id"=8 OR "intnum"=80`.
    *   Complex nested expressions: `"intnum"=80 AND ("id"<5 OR "id">50)`.
    *   Negated groups: `"char_null"='80' AND NOT ("char"<'5' OR "char">'50')`.
    *   Blank-Q combinations: `"id">5`.
    *   Annotation-resolved filters: `"id">5 OR "intnum"<5`.

*   Handle blank Q objects:
    *   When combined with a non-blank Q using AND or OR, ensure the resolved criterion equals the non-blank side only, reflecting only the non-blank Q's condition in the SQL.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.