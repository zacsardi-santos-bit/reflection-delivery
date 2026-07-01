## Description

The Spanner Go client library emits distributed tracing spans for operations like read-write transactions and row iteration, but these spans are missing important contextual attributes. Currently, when a developer instruments their application with distributed tracing and attaches transaction or request tags to their Spanner operations, those tags do not appear on the corresponding trace spans. Additionally, the spans lack standard client identity attributes such as the database name, instance name, client library version, repository, and artifact identifiers.

This makes it difficult to correlate trace data with specific Spanner operations and to identify which client version or database was involved when investigating issues.

## Expected Behavior

- Tracing spans for read-write transactions should include the database name, instance name, client region, client version, client repository, client artifact identifier, and the transaction tag (if one was set).
- Tracing spans for row iteration should include the statement tag and the SQL query text when those are present in the query options, as well as the enclosing transaction tag.
- A new set of tracing utility functions should be introduced in the spanner package to standardize how spans are started and ended, and how request-level attributes (tags and SQL statements) are extracted from RPC request objects and attached to the active span.
- The tracing utilities must short-circuit efficiently for non-recording spans to avoid unnecessary allocation overhead.

## Why This Matters

Without these attributes on spans, developers have no way to connect a slow or failing trace back to the specific transaction tag or query that caused the problem. Enriching spans with tags and client identity information enables much more powerful analysis in tracing backends and reduces mean time to resolution for Spanner-related performance issues.
