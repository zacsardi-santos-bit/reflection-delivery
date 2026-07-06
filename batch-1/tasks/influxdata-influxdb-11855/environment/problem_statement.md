## Description

When building features like audit logs, authorization descriptions, or any UI that needs to display a resource's name given only its type and identifier, developers currently have to know which specific service to call for each resource type. There is no single place to resolve "given this resource type and ID, what is its name?"

We need a unified name-lookup capability on the key-value service so that any supported resource type can be resolved to its human-readable name through a single interface.

## Expected Behavior

- Buckets, dashboards, organizations, sources, telegraf configurations, and users should all be resolvable to their names by passing the resource type and ID to a single lookup method.
- Resource types that do not carry meaningful names (such as authorizations and tasks) should return an empty string without an error.
- If the provided ID is invalid or the resource type is unrecognized, an error should be returned.
- If no resource exists with the given ID for a type that does support names, an error should be returned.
- The helper functions that create test store instances should return the store interface type rather than a concrete implementation type, so that they can be used interchangeably in tests that exercise this new lookup capability.

## Why This Matters

Without this, every caller that wants to display a resource name must maintain its own dispatch table over resource types. Centralizing this into the key-value service reduces duplication and makes it easy to add name resolution for new resource types in the future.
