## Description

When import plugins convert existing infrastructure state, they return a list of resources to import. Currently, each resource in that list can only carry a type, name, ID, version, and plugin download URL. However, there are scenarios where a resource being imported is a component resource (not a leaf resource), or is a remotely-hosted component, or should be imported under a logical name that differs from its declared name. Without these properties, the engine cannot correctly handle those cases.

## Expected Behavior

- Each resource returned by an import plugin during state conversion should be able to carry a logical name — a name that may differ from the resource's declared name and is used for identification purposes.
- Each resource should be able to indicate whether it is a component resource.
- Each resource should be able to indicate whether it is a remote component (relevant only when the resource is a component).
- These properties should be correctly passed through both the client-side plugin wrapper and the server-side plugin host, so the engine receives all necessary information.

## Why This Matters

Import plugins that need to express component resources, remote components, or custom logical names currently have no way to do so. This limits the types of infrastructure that import plugins can accurately describe and migrate into Pulumi management. Adding these fields removes that limitation and allows import plugins to fully describe the resources they surface.
