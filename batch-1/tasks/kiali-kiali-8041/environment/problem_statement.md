## Description

The service graph API currently uses a cryptographically weak hashing algorithm to generate unique identifiers for nodes and edges in graph responses. This algorithm is considered insecure for modern use and produces relatively short identifiers (32 hex characters) that have higher collision probability than alternatives. We should switch to a stronger algorithm that produces longer, more collision-resistant identifiers (64 hex characters) to improve security and uniqueness guarantees.

This affects identifier generation in several parts of the codebase: the graph Cytoscape configuration layer, the Istio telemetry data processing layer (both the main pipeline and the extensions appender), and the equivalent components in the mesh graph API.

## Expected Behavior

- Node identifiers in graph API responses are 64-character hexadecimal strings (instead of 32-character strings)
- Edge identifiers in graph API responses are 64-character hexadecimal strings
- The same change applies to the mesh graph API responses
- All existing golden test data files are updated to reflect the new identifier values
- The golden test data files do not have a trailing newline at the end — they end with the closing brace of the JSON object

## Why This Matters

Using a weak hashing algorithm for IDs is a security concern, and collision resistance is important for graph correctness (two different nodes or edges should never share an ID). Upgrading to a stronger algorithm addresses both concerns. The test infrastructure also had a related cleanup opportunity: a workaround that stripped the last byte from golden files before comparison is removed, making the golden files easier to create, maintain, and understand.
