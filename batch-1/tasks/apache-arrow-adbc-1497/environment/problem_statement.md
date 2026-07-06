## Description

The Go ADBC drivers should be upgraded to use a newer major version of the Arrow Go library. Currently, the entire codebase references an older major version of the Arrow Go library, and it needs to be updated to the next major version throughout all packages and drivers.

Additionally, there is a bug in the FlightSQL driver's cookie handling: when the server returns a flight endpoint that points to a different location (causing the driver to open a secondary connection to that address), the session cookies from the original connection are not propagated to the secondary connection. This means that any workload relying on cookie-based session tracking breaks silently when servers use location-based routing to direct clients to retrieve data from a different host.

## Expected Behavior

- All driver code and packages in the Go module should import from the new Arrow Go major version.
- The vendor arrow version reported by the FlightSQL driver should reflect the upgraded library version.
- When the FlightSQL driver creates a sub-connection for a flight endpoint with a non-empty location URI, it must carry over any active session cookies from the primary connection to the sub-connection.
- Existing authentication tokens should be reused for sub-connections rather than triggering redundant re-authentication.

## Why This Matters

Staying on an outdated major version of a core dependency blocks access to bug fixes, performance improvements, and new features in the Arrow library. The cookie propagation bug means that real-world FlightSQL servers which direct clients to separate endpoints for data retrieval (a common pattern for distributed query execution) will experience broken session handling when cookies are enabled.
