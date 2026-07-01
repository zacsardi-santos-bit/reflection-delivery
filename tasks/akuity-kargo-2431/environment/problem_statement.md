## Description

After upgrading a Kubernetes controller framework dependency to a newer major version, the promotions controller package no longer compiles. The newer version of the framework replaced its untyped work queue interfaces with typed, generic equivalents that carry specific item types. All event handler implementations in the promotions controller still reference the old untyped interface, causing build failures and preventing any tests in the package from running.

## Expected Behavior

- All event handler methods in the promotions controller that interact with work queues should use the new typed work queue interface parameterized with reconcile requests.
- The handlers must continue to produce the correct reconcile request enqueue behavior they had before — correctly enqueuing items when relevant ArgoCD Application events occur and skipping enqueues when conditions are not met (missing objects, shard selector mismatches, no indexed promotions).

## Why This Matters

The entire promotions controller package is broken due to this API incompatibility. No promotions-related reconciliation can be deployed or tested until the event handlers are updated to use the new typed work queue interface. This blocks both forward progress on the upgrade and CI for the promotions subsystem.
