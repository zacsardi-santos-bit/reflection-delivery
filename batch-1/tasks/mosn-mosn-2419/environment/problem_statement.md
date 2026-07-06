## Description

MOSN's flow control stream filter currently only supports rules that are statically defined in the configuration file. This means that whenever operators need to change rate-limiting rules, they must modify config files and restart the proxy — there is no way to update rules dynamically at runtime.

We need to support **dynamic data sources** so that flow control rules can be loaded from an external configuration center. The filter should be able to connect to such a service at startup, fetch the current rules, and receive rule updates without requiring a restart.

Additionally, the rate-limiting library this component depends on has been upgraded to a newer version that changed its API for constructing error objects. The existing code and tests use the old API, which causes compilation failures across the entire flow control package.

## Expected Behavior

- A new pluggable data source abstraction is introduced, with an initial implementation for a popular configuration center (Nacos), so flow control rules can be loaded and updated dynamically.
- The flow control filter configuration should support specifying one or more dynamic data resources by type and connection parameters.
- All existing flow control tests should compile and pass after the library upgrade.

## Why This Matters

In production environments, operators must be able to adjust rate limiting rules without service interruptions. Static configuration makes this impractical. Dynamic data source support enables real-time rule management and better operational control.
