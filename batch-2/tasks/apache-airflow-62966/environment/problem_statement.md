## Description

The Azure Synapse Pipeline hook currently only supports synchronous operation. There is no asynchronous variant available, which means it cannot be used with Airflow's deferrable operators or in any async execution context. This gap makes it impossible to build non-blocking integrations with Azure Synapse Pipelines.

## Expected Behavior

- An async-capable hook for Azure Synapse Pipelines should be available alongside the existing synchronous hook.
- The async hook should support both client secret authentication (using a client ID, client secret, and tenant ID) and default Azure credential authentication (supporting managed identity and workload identity configurations).
- When client secret credentials are provided but the required tenant ID is missing, the hook should raise an informative error indicating that the tenant ID is required.
- The async hook should expose a method to retrieve the current status of a pipeline run as a string.
- The async hook should support refreshing its underlying connection.
- The async hook should properly clean up its underlying connection when closed, including setting the internal connection reference back to a null state.
- The async hook should be usable as an async context manager, automatically closing the connection upon exit.

## Why This Matters

Without an async hook, operators that need to defer execution while waiting for a Synapse pipeline to complete must block a worker thread. An asynchronous hook enables deferrable operators to release their worker thread and resume only when the pipeline finishes, improving resource utilization in Airflow deployments.
