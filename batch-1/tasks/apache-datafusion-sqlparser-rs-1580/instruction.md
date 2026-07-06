Implement support for table sampling syntax in a Rust SQL parser library. Ensure the parser can handle various sampling forms and dialect-specific syntax placements, enabling it to parse and serialize SQL statements with sampling clauses correctly.

*   Update the `TableFactor::Table` enum variant:
    *   Add a new optional field `sample` of type `Option<TableSampleKind>`, defaulting to `None`.

*   Implement the `table_from_name` function in `src/test_utils.rs`:
    *   Signature: `pub fn table_from_name(name: ObjectName) -> TableFactor`
    *   Return a `TableFactor::Table` with the given `name` and all optional fields set to defaults: `alias: None`, `args: None`, `with_hints: vec![]`, `version: None`, `partitions: vec![]`, `with_ordinality: false`, `json_path: None`, `sample: None`.

*   Update existing helper functions in `src/test_utils.rs`:
    *   Ensure `table` and `table_with_alias` include `sample: None` in the returned `TableFactor::Table`.

*   Modify the `Dialect` trait:
    *   Add method `supports_table_sample_before_alias(&self) -> bool` with default implementation returning `false`.
    *   Override in `HiveDialect` to return `true`.

*   Update the parser to support:
    *   `TABLESAMPLE` and `SAMPLE` keywords as table sample introducers.
    *   Ensure these keywords are added to the `RESERVED_FOR_TABLE_ALIAS` list.

*   Ensure parsing and round-tripping of SQL statements:
    *   When `supports_table_sample_before_alias` is `true`:
        *   Parse statements like `SELECT * FROM tbl TABLESAMPLE (50) AS t`.
    *   When `supports_table_sample_before_alias` is `false`:
        *   Parse statements like `SELECT * FROM tbl AS t TABLESAMPLE BERNOULLI (50)`.

*   Ensure dialect-specific parsing and round-tripping:
    *   ClickHouse: Handle `SAMPLE` with fractional, integer, and ratio expressions.
    *   Hive: Support bucket sampling, byte-size sampling, percentage, and row count sampling.
    *   Snowflake and generic dialects: Support various `SAMPLE` and `TABLESAMPLE` expressions with methods and seeds.

*   Publicly export types from `sqlparser::ast`:
    *   `TableSample`, `TableSampleBucket`, `TableSampleKind`, `TableSampleMethod`, `TableSampleModifier`, `TableSampleQuantity`, `TableSampleSeed`, `TableSampleSeedModifier`, `TableSampleUnit`.

*   Update `src/ast/spans.rs`:
    *   Include the `sample` field in `TableFactor::Table` span handling, ignoring it in span computation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.