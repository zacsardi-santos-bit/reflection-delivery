## Description

Currently, when updating deploy targets for an application, the system accepts a flat list of target names. This works fine for single-plugin setups, but it becomes inadequate when multiple deployment plugins are involved, because there is no way to specify which targets belong to which plugin.

We need to change the data model so that deploy targets are organized per plugin. Instead of a simple list of target names, the system should accept a mapping from plugin name to that plugin's list of deploy targets. This allows each plugin to independently manage its own set of targets.

## Expected Behavior

- When updating an application's deploy targets, callers provide a map where each key identifies a plugin and each value is the list of targets for that plugin.
- All internal interfaces and their implementations (including test mocks) must reflect this new structure.
- The gRPC API server layer must accept the new plugin-keyed format when processing deploy target update requests.

## Why This Matters

As the platform evolves to support multiple deployment plugins, each with its own set of infrastructure targets, the old flat-list approach creates ambiguity. Organizing deploy targets by plugin makes the data structure expressive enough to represent real multi-plugin deployments correctly.
