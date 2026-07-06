## Description

When the Airflow Redis provider hook opens a connection to Redis, it does not identify itself to the server. This means that on the Redis server side, there is no way to tell that a connection was established by the Airflow Redis provider rather than some other client. In environments with multiple services connecting to the same Redis instance, this makes connection tracking, debugging, and monitoring significantly harder.

## Expected Behavior

- When establishing a Redis connection, the hook should pass client identification metadata so the Redis server can attribute the connection to the Airflow Redis provider.
- The hook should use the most capable client identification method available in the installed Redis client library. If a structured driver-info object is available, it should be used. If that is not available but the library supports a simpler name-based identification, that should be used instead. If neither is supported, no identification metadata should be sent.
- The identification metadata should include the name of the Airflow Redis provider.
- Existing connection parameters (credentials, SSL settings, etc.) must not be affected by this change.

## Why This Matters

Without client identification, Redis server administrators cannot distinguish Airflow-originated connections from connections made by other services or libraries. This feature makes it easier to audit, trace, and debug Redis usage in multi-tenant environments where multiple applications share the same Redis server.
