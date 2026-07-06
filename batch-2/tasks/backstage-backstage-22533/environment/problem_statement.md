## Description

There is a bug in the Backstage frontend framework where the components API breaks when the frontend plugin API package is installed more than once (e.g. through transitive dependencies resolving to different versions). When this happens, a component reference object from one installation is not recognized by a component registered via a reference from another installation, even though both references describe the same component type. Components appear to be missing even though they were registered correctly.

## Expected Behavior

- Component lookups should be based on the component's unique identifier, not on object identity of the reference itself. Two separate reference objects pointing to the same component type (sharing the same ID) should always resolve to the same registered component.
- The components API implementation should be initializable directly from the application's extension tree, without requiring the caller to manually extract and wire together component registrations.
- The utility function used to resolve app node specifications from a list of features should not require callers to supply extension lists or parameter lists that are irrelevant to their use case — these should be optional and default to empty.

## Why This Matters

In real-world Backstage deployments, duplicate package installations are common and hard to avoid. When component registration silently fails because of object identity mismatches between two copies of the same reference type, the application breaks in confusing and hard-to-debug ways. Making component resolution ID-based ensures robustness regardless of how many times a package is installed.
