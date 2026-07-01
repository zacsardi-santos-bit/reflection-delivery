## Description

SQLFluff's Postgres dialect does not support materialized view statements, causing the parser to fail on any SQL file that creates, modifies, drops, or refreshes a materialized view. This is a common PostgreSQL pattern and the lack of support means large real-world databases using materialized views cannot be linted at all.

## Expected Behavior

The following categories of statements should parse cleanly in the Postgres dialect with an accurate parse tree:

- Creating a materialized view, including with and without the "if not exists" guard, with storage options, with a parenthesized or bare query body, and with or without a data-population clause
- Altering a materialized view — covering column attribute changes, storage settings, clustering, tablespace reassignment, ownership changes, dependency declarations, renames, schema moves, and the "all in tablespace" bulk reassignment form
- Dropping a materialized view — including multiple views in one statement, with or without "if exists", and with cascade or restrict behavior
- Refreshing a materialized view — including the concurrent form and with or without the data clause

Additionally, the data-population clause ("WITH DATA" / "WITH NO DATA") that can appear at the end of certain creation statements should be recognized and grouped as a unified parse element rather than left as loose keywords. This same grouping should apply to the existing table-creation-from-query statement.

## Why This Matters

Materialized views are a fundamental PostgreSQL feature used widely for performance optimization. Without parsing support, users cannot lint any SQL that manages materialized views, which makes SQLFluff impractical for many Postgres-heavy projects. Additionally, a typo in an existing test fixture filename should be corrected as part of this cleanup.
