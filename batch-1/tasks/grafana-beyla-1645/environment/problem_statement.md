## Description

When Beyla instruments a service that communicates with a PostgreSQL database, it sometimes fails to capture and report SQL query telemetry even though the traffic is clearly database traffic. This happens because the SQL validation logic requires both a SQL operation keyword **and** a table name to be successfully extracted from the raw network bytes before the traffic is accepted as a valid SQL span.

For many legitimate PostgreSQL queries — especially those using complex join expressions with quoted identifiers, or certain prepared statement formats — the table name extraction can fail even though the SQL operation is clearly present. The result is that those queries are silently dropped rather than being reported as database spans.

## Expected Behavior

- When Beyla has already identified that a connection is communicating with a PostgreSQL server, it should accept SQL traffic as valid if at least the SQL operation (e.g., SELECT, INSERT) is found, even if the target table could not be extracted.
- For generic/unknown TCP connections, the stricter requirement (both operation and table must be found) should remain to reduce false positives.
- The PostgreSQL query parser should correctly extract table names from queries that use double-quoted identifier notation for table and column names, including queries that join multiple tables.

## Why This Matters

Users relying on Beyla to observe database performance will see gaps in their telemetry — certain query patterns simply never appear in traces or metrics. Making the validator aware of the database context (PostgreSQL vs. unknown) reduces these false negatives without compromising correctness for unidentified connections.
