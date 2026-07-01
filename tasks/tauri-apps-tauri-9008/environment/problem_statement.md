## Description

The ACL permission manifest type is currently nested under the plugin module, which is not an intuitive location for it. It should live in its own dedicated module so that it's easier to discover and import correctly. Additionally, the function that processes plugin permission definition files during the build step does not support any filtering — it always processes every permission file it finds, with no way to selectively include or exclude specific permissions.

## Expected Behavior

- The permission manifest type should be importable from a dedicated manifest module within the ACL namespace, rather than from the plugin module.
- The function responsible for processing permission files should accept a predicate/filter parameter that lets callers control which permissions are included. Passing a filter that always returns true should preserve the current behavior of including everything.

## Why This Matters

Reorganizing the manifest type into its own module makes the ACL API more navigable. Adding a filter capability to the permission processing function allows applications to have fine-grained control over which permissions are exposed, which is important for scenarios where only a subset of available permissions should be included in a build.
