## Description

The RBAC authorization plugin needs several improvements to support more flexible policy matching and to simplify its API.

First, the metadata list matcher currently hard-codes a single filter namespace when matching metadata. It should instead accept an explicit filter name so that different filter namespaces can be targeted — for example, targeting the authentication filter's metadata for JWT claim checks and other filter namespaces for custom protocol-level attributes.

Second, the TCP and HTTP filter-building functions currently accept a separate boolean parameter indicating the proxy version, which determines which config encoding format to use. This per-call version detection adds complexity at every call site. These functions should instead drop the version parameter and always produce filters encoded in the struct-based format, simplifying both the API and the call sites.

Third, there is no support for policies that match against metadata produced by arbitrary network-layer filters (such as a MySQL proxy). Authorization policies should be able to reference metadata from any Envoy filter using a conventional key naming scheme that uses an experimental prefix followed by the filter name and a bracketed metadata key. When the constraint value is itself bracketed, a list-based metadata match should be generated; otherwise, a string-based match should be generated.

## Expected Behavior

- The metadata list matcher accepts the filter name as an explicit argument rather than hard-coding it.
- TCP and HTTP filter builders take only the service and options arguments, with the TCP/HTTP distinction managed internally via the options struct.
- Authorization constraint keys that follow the experimental filter key naming convention are recognized and converted into the appropriate metadata matchers.
- A helper that recognizes the binary key format — text before the opening bracket, non-empty content inside the brackets, ending with a closing bracket — is available for this purpose.

## Why This Matters

Without these changes, it is impossible to write authorization policies that enforce access control based on protocol-specific connection metadata (such as which database tables a client accesses through a MySQL proxy). The API simplification also removes fragile per-call version detection.
