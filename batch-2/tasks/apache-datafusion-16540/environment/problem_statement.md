## Description

When generating SQL output targeting BigQuery from a DataFusion logical plan, column aliases that contain special characters (such as parentheses, asterisks, at-signs, and similar punctuation) result in invalid SQL. BigQuery has strict rules about which characters are allowed in column identifiers, and many characters that are perfectly valid in generic SQL aliases are rejected.

Currently, the unparser has no way to produce BigQuery-compatible SQL — it will emit aliases verbatim, which can produce SQL that BigQuery will refuse to parse or execute.

## Expected Behavior

- A BigQuery-specific SQL dialect should be available in the unparser so that SQL can be generated specifically for BigQuery targets.
- When this dialect is used, any special characters in column aliases should be automatically converted to a safe encoded representation using the character's decimal Unicode code point preceded by an underscore — for example, an opening parenthesis becomes underscore-40, a closing parenthesis becomes underscore-41, an asterisk becomes underscore-42, and an at-sign becomes underscore-64.
- This encoding should apply both to alias definitions in SELECT clauses and to any references to those aliases elsewhere in the query (such as in WHERE conditions), so the encoded form is consistent throughout the generated SQL.
- The dialect should also use BigQuery's preferred backtick quoting style for identifiers.

## Why This Matters

Developers using DataFusion to generate SQL for BigQuery pipelines may have logical plans whose column names or computed expression aliases include characters that BigQuery does not support. Without a BigQuery-aware dialect, there is no automated way to sanitize these names, forcing manual post-processing of generated SQL or causing failures at query execution time.
