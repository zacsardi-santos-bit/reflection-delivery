## Description

Apache Pinot supports Groovy as an embedded transform function language inside SQL queries. While this provides powerful data transformation capabilities, there is currently no security barrier preventing malicious or misconfigured Groovy scripts from executing dangerous operations — such as running shell commands, calling system-level functions, or importing arbitrary Java classes. This is a significant security risk in any deployment where query inputs are not fully trusted.

## Expected Behavior

- A configurable static analysis layer should be added that inspects Groovy scripts before they are executed.
- The analyzer should enforce a policy based on lists of allowed class receivers, allowed imports, allowed static imports, and disallowed method names. The policy should also control whether method definitions are permitted.
- Default allowed-receiver and allowed-import lists should be provided as a starting point.
- The configuration must survive a JSON round-trip (serialize and deserialize) without any loss of data, so it can be stored and distributed as part of system configuration.
- Scripts that attempt to execute shell commands, call system exit, access dangerous class hierarchies via indirect imports, or invoke disallowed methods must be rejected with descriptive error messages.
- The configuration must be settable and resettable at runtime — changes must take effect immediately without restarting the service.
- The existing query validation mechanism that can globally disable all Groovy usage should be updated to also support the new static analysis path. When Groovy is globally disabled, queries containing Groovy functions should be rejected with a clear "disabled" error message. When Groovy is allowed, queries should be subject to static analysis checks.
- Groovy function calls in a query must have at least 2 arguments; fewer should be rejected with an appropriate error.

## Why This Matters

Without this change, any user who can submit queries to Pinot can embed Groovy scripts that call shell commands or invoke arbitrary Java code — potentially exfiltrating data, crashing the server, or gaining unauthorized access to the host system. This change closes that attack surface by adding a policy-driven static analysis checkpoint before any Groovy script is executed.
