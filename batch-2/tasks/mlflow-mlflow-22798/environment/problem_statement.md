## Description

When using Databricks tracing with a SQL warehouse backend, the warehouse may not be running at the moment a trace operation is attempted — it could be stopped, stopping, or in the process of starting. Currently, tracing operations such as retrieving, searching, creating, or annotating traces simply fail if the warehouse isn't already in an active state. Users have no built-in way to let MLflow handle this transparently.

## Expected Behavior

- Before making any tracing API call that targets a SQL warehouse (such as fetching trace info, searching traces, managing assessments, or creating trace locations), MLflow should automatically check whether the warehouse is running and start it if it isn't.
- If the warehouse is already running, the operation should proceed immediately without any unnecessary delay.
- If the warehouse is in a stopped or transitional state, MLflow should start it and wait for it to become active before continuing.
- The auto-start behavior should be configurable: users should be able to disable it entirely via an environment variable.
- The wait timeout should also be configurable via an environment variable, with a sensible default.
- A caching mechanism should prevent redundant warehouse status checks from being made on every single call within a short period.
- Operations that use different API paths and do not require a warehouse (such as logging raw spans or fetching online trace details) should not be affected by this feature.

## Why This Matters

Without this feature, any lapse in warehouse activity causes tracing operations to fail with unhelpful errors. Automatically managing warehouse lifecycle removes a significant operational burden from users who use Databricks SQL warehouses as their tracing backend, making MLflow tracing more robust and self-healing in production workflows.
