## Description

The Trino client library currently relies on Java's database connectivity infrastructure for connection URI parsing and validation. This creates friction for users of the client in non-JDBC contexts: parsing a connection URI can throw checked database-driver exceptions that callers must handle even when they have nothing to do with database drivers. Additionally, when multiple connection properties are misconfigured at once, the current code reports only the first error and stops — users have to fix one issue at a time and retry repeatedly to discover all problems.

## Expected Behavior

- Connection validation errors should be thrown as ordinary unchecked exceptions rather than checked database exceptions, making the client library usable in non-JDBC contexts without awkward try/catch boilerplate.
- When multiple connection properties are invalid simultaneously, all validation errors should be collected and reported together in a single exception message, rather than stopping at the first failure.
- There should be a programmatic builder API for constructing connection URIs with type-safe property setters, and each connection property should support encoding its typed value to a string and decoding a string back to the typed value (bidirectional serialization).
- The collection of all known connection properties should be accessible via a single static method so callers can enumerate them.
- The CLI's query runner should no longer accept a network-logging parameter in its constructor, since logging configuration is managed through the connection properties system.
- The CLI options class should have every non-CLI-specific field explicitly mapped to its corresponding connection property, making the mapping exhaustive and verifiable.
- Port 443 in a connection URL should continue to default to enabling SSL, and the SSL validation logic should correctly account for this default — SSL-specific configuration should be allowed when SSL is active (including via port 443's implicit default) and rejected only when SSL is explicitly disabled.
- When a connection property is specified both in the URL and in the properties argument, the error should clearly indicate that the property was provided in both places.

## Why This Matters

Users of the Trino client outside a JDBC context should not be forced to handle checked SQL exceptions when those exceptions have no semantic meaning in their context. Collecting all validation errors at once provides a much better developer experience — users know upfront what they need to fix instead of iterating through errors one at a time. The new builder API enables clean programmatic construction of connection URIs from typed values.
