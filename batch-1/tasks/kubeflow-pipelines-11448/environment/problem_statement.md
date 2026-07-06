## Description

The backend codebase depends on Kubernetes client libraries that have been upgraded to newer versions. This upgrade introduced several breaking changes that cause compilation failures and test failures across multiple packages.

Specifically, the following areas are affected:

- The mechanism for registering event handlers with Kubernetes informers has changed: it now returns a registration handle and an error instead of returning nothing. All interfaces and concrete implementations that wrap this behavior must be updated to match.
- Kubernetes informer event callbacks for object additions now include an extra parameter that indicates whether the notification occurred during initial list synchronization. Any code that implements or invokes these callbacks must be updated.
- The type used to declare storage resource requirements for persistent volume claims in ephemeral volume configurations has been renamed to be more specific. Code constructing these volume specifications must use the new type name.
- Condition status types used in scheduled workflow conditions were previously imported from an internal Kubernetes package that is not intended for external use. These must now be imported from the standard public Kubernetes API package.

## Expected Behavior

- Event handler registration returns a valid registration handle along with a nil error on success.
- Event handler callbacks for additions compile and run with the updated parameter signature.
- Persistent volume claim resource requirements in ephemeral volume specs use the correct updated type.
- Scheduled workflow condition status fields and constants use the public Kubernetes API package.

## Why This Matters

Without these updates, the backend fails to compile against the upgraded Kubernetes dependency versions and all associated tests fail. This blocks development and CI pipelines that rely on the updated libraries.
