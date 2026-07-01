## Description

The storage extension for persisting collector state has lived under an "experimental" namespace for a long time, but the API is now mature enough to graduate to a stable home. Other cross-cutting extension packages in this project have already moved to an "x" naming convention, and the storage extension should follow the same pattern to signal that it is a supported, stable API.

Additionally, the way storage operations are currently represented is unnecessarily confusing. Operations are exposed as an opaque pointer-type alias over an unexported struct, which makes it harder for downstream implementors to work with them. Switching to a plain exported struct with exported fields makes the interface simpler and more idiomatic.

## Expected Behavior

- A new module exists at a stable path following the project's established naming convention for cross-component packages, containing the storage functionality previously found in the experimental namespace.
- The storage client interface's batch operation method accepts operations as plain pointers to a concrete struct, not through an opaque alias type.
- All module dependency manifests across the repository that previously referenced the experimental storage path are updated to reference the new stable path instead.
- Builder configuration files are updated to include replace directives for the new module path.
- The old experimental package is kept but deprecated with forwarding aliases, ensuring backward compatibility for existing users.

## Why This Matters

Keeping stable APIs under an "experimental" label creates confusion about their maturity. Moving to the stable "x" namespace aligns with project conventions and makes it clear that the storage extension is a first-class, supported component. The API simplification reduces friction for developers building custom storage backends.
