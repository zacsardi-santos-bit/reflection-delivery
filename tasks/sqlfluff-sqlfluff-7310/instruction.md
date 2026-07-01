Update the BigQuery SQL parser to correctly handle type conversion expressions with format specifiers. Ensure that the format specifier is recognized as a keyword and the format string is a direct quoted literal in the parse tree. Support various conversion patterns, including optional timezone specifications.

Requirements:
*   Modify the `FunctionContentsGrammar` in `src/sqlfluff/dialects/dialect_bigquery.py` to:
    *   Recognize the FORMAT keyword as a keyword token in the parse tree.
    *   Parse the format string as a direct quoted literal node.
    *   Support CAST expressions with the pattern: `CAST(<expression> AS <datatype> FORMAT '<format_string>')`.
    *   Allow an optional AT TIME ZONE clause after the format string, producing a `time_zone_grammar` node.
*   Ensure the parser supports conversions between types including STRING, BYTES, DATE, TIME, and TIMESTAMP.
*   Update the SQL fixture file `test/fixtures/dialects/bigquery/select_with_cast.sql` to include:
    *   Examples of CAST FORMAT patterns: string to BYTES, DATE to STRING, TIME to STRING, TIMESTAMP to STRING with AT TIME ZONE, string to TIME, column reference to STRING with numeric format patterns, positive and negative numeric literals to STRING.
*   Update the YAML fixture file `test/fixtures/dialects/bigquery/select_with_cast.yml` to reflect:
    *   FORMAT as a `keyword` node and the format string as a `quoted_literal` node.
    *   The AT TIME ZONE clause as a `time_zone_grammar` node with AT, TIME, ZONE keywords followed by the timezone expression.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.