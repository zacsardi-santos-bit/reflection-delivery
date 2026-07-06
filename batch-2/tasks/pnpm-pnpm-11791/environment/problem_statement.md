## Description

The dependency resolver does not currently factor in configured dependency patches when building the resolved dependency tree. As a result, the resolved package identities in the tree do not reflect which patches have been applied, and there is no record of which configured patches were actually matched against resolved packages.

## Expected Behavior

- When a project has configured patches for certain dependencies, the dependency resolver should integrate that patch information during resolution.
- A resolved package whose version matches a configured patch (either by exact version or by a satisfying version range) should have its identity updated to include a fingerprint of the applied patch.
- The resolver should track which configured patches were actually matched so that downstream tooling can detect patches that were configured but never applied.
- When a resolved package version simultaneously satisfies multiple configured range-based patches, the resolver should fail with an explicit conflict error rather than silently choosing one patch.
- When no configured patch matches any resolved package, identities remain unchanged and no patches are recorded as applied.

## Why This Matters

Without this integration, the resolved dependency tree is unaware of patches, making it impossible to correctly deduplicate patched and unpatched versions of the same package or to reliably detect unused patch configurations. Integrating patch awareness into the resolution phase ensures that patched packages are treated as distinct from their unpatched counterparts throughout the entire install pipeline.
