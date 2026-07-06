## Description

The Cloud Composer provider currently uses a single, fixed REST API version when making calls to remote Airflow environments, regardless of which version of Airflow is actually running in that environment. This means that operations that interact with Cloud Composer environments running Airflow 3 or later will issue requests to the wrong API endpoint version, leading to failures.

## Expected Behavior

- When interacting with a Cloud Composer environment running Airflow 3 or later, the provider should route requests to the version 2 of the REST API.
- When interacting with environments running Airflow versions below 3, the provider should continue using the version 1 REST API.
- This version-aware routing should apply consistently across all relevant operations: triggering DAG runs, fetching DAG runs, and fetching task instances — both in synchronous and asynchronous code paths.
- Additionally, there is currently a parameter in the DAG run sensor and trigger classes that controls whether to use the REST API at all. Since the REST API is now always used, this parameter is obsolete. Passing it should produce a deprecation warning rather than silently influencing behavior, and it should no longer be reflected in the trigger's serialized state.

## Why This Matters

Users running Cloud Composer environments on Airflow 3 cannot reliably use the existing trigger, sensor, and hook classes because all requests are sent to the wrong API version. Fixing the version-aware routing makes the provider compatible with both older and newer Cloud Composer environments. Deprecating the now-unnecessary toggle prevents future confusion and prepares users for its eventual removal.
