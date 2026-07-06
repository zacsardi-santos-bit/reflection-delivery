## Description

The Pulumi Configuration Language (PCL) has no way to express resource lifecycle hooks — named commands that should execute at specific points in a resource's lifecycle, such as before or after creation, updates, or deletion. There is also no mechanism to attach those hooks to specific resources.

## Expected Behavior

- Developers should be able to declare named hook blocks in a PCL program, each specifying a command to run and optionally whether the hook should execute during preview operations.
- Inside the hook command, the author should be able to reference runtime context about the resource being operated on (its identifier, name, type, and input/output snapshots). This runtime context must not be available in the preview condition.
- A hook block must only allow recognized attributes. Unrecognized attributes should produce a clear error message listing the supported ones.
- Resources should be able to reference declared hooks via a lifecycle hooks configuration in the options block, associating them with recognized lifecycle event names.
- The binder must validate that only known lifecycle event names are used, that the lifecycle hooks configuration in the options block has the correct object structure, and that each lifecycle entry maps to a list of hook references.
- Attempting to attach a hook with an unrecognized lifecycle event name, or specifying the lifecycle hooks configuration in the options block as a non-object, or specifying a lifecycle entry as a non-list value, should all produce descriptive diagnostic errors.
- A program that successfully binds hooks should expose those hooks as a retrievable collection on the program object.

## Why This Matters

Without lifecycle hook support, there is no standardized way in PCL to express pre/post-operation side effects for resources. Adding this capability allows infrastructure programs to attach commands to resource events in a validated, type-checked way.
