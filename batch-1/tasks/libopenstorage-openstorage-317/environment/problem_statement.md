# Simplify Configuration Manager API

## Description

The current configuration manager requires callers to pass a lifecycle context when creating an instance. This makes the API more complex than necessary for most use cases — users just want to connect to the storage backend and start reading, writing, and watching configuration values. The context management overhead shouldn't be the user's responsibility.

Additionally, the current callback/watch mechanism doesn't reliably invoke registered callbacks when configuration values change. Users who register a callback to watch for cluster or node configuration updates cannot depend on that callback being called promptly after a change is written.

## Expected Behavior

- Creating a configuration manager should only require a handle to the storage backend — no context parameter
- Setting a cluster configuration value and then retrieving it should return data that is identical to what was stored
- Setting a node configuration value and then retrieving it by node ID should return data identical to what was stored
- Registering a named callback to watch for cluster configuration changes should cause that callback to be invoked with the new configuration whenever a cluster config is written
- Registering a named callback to watch for node configuration changes should cause that callback to be invoked with the new configuration whenever a node config is written

## Why This Matters

These changes make the configuration management API easier to consume. Developers can now create a manager with minimal boilerplate, rely on get/set round-trips returning consistent data, and trust that registered watchers will be triggered reliably on changes — enabling reactive configuration workflows.
