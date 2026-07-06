## Description

Several independent bugs and missing features have been identified in MLflow that need to be addressed:

1. **OpenAI integration crashes on non-standard tool values**: When using MLflow's OpenAI autologging with certain OpenAI SDK configurations, the tools parameter can contain a special "not-given" placeholder value rather than a proper list. This causes an unhandled error during trace logging. The tool parsing logic should gracefully handle any non-iterable value for tools and treat it as an empty tool list.

2. **Unity Catalog model registration doesn't support the authentication policy resource format**: Models registered in Unity Catalog can specify their resource dependencies (serving endpoints, vector search indexes, functions, connections, tables) either via a top-level resources field or via a newer authentication policy structure. Currently, only the top-level resources format is recognized when extracting model dependencies, causing models configured with the newer authentication policy format to have their dependencies silently ignored.

3. **Async trace export queue drops pending tasks on process exit**: When a process that uses the async trace export queue exits before all enqueued tasks finish, some tasks may be dropped rather than completed. The queue should ensure all pending tasks are executed, even during process shutdown when the thread pool may be unavailable.

4. **Logging configuration environment variable rename with backward compatibility**: The environment variable that controls MLflow's logging configuration should be renamed to follow the project's naming conventions. The old name should continue to work but emit a deprecation warning.

## Expected Behavior

- Passing a non-list, non-iterable value as the tools parameter produces an empty tool list rather than an error
- Models with an authentication policy resource configuration are properly recognized when registering to Unity Catalog
- All pending trace export tasks complete even when the submitting process exits before they finish
- Both the old and new logging environment variable names correctly configure the mlflow logger level

## Why This Matters

These fixes prevent trace data loss and crashes in production MLflow deployments, improve compatibility with newer model configuration formats in Unity Catalog, and align environment variable naming with the project's conventions.
