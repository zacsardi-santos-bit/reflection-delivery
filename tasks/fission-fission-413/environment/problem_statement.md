## Description

When Fission deploys runtime and builder pods for an environment, it constructs the container specifications entirely from internal logic. There is currently no mechanism for users to provide custom container-level configuration — such as environment variables — through the environment specification. This is a significant limitation when users need to control settings like environment variables for their language runtime or builder containers.

## Expected Behavior

- The environment specification should support an optional container customization section for both the runtime and the builder components.
- When environment pods are deployed, any user-supplied container customization should be merged with the system-defined container spec. The system's values should take priority for fields it controls (e.g., name, image, pull policy), but user-provided fields that the system leaves unset (such as environment variables) should be honored.
- A general-purpose merge utility should exist that combines multiple container specifications into one using a priority order: earlier specs win for scalar fields, while list-type fields (like environment variables) are combined from all specs in order.

## Why This Matters

Users running Fission functions often need their runtime or builder containers to have specific environment variables set — for example, to configure language runtimes, connect to services, or enable debug flags. Without this feature, there is no supported way to pass these settings through the environment definition, forcing workarounds that are fragile or unsupported.
