## Description

We have decided to remove LoongArch64 on Linux from the set of officially supported target platforms in our build and CI/CD infrastructure. Currently the pipeline platform matrix configuration files still list this architecture as a build and test target, and the Docker-based development workflow documentation still describes it as a cross-compilation option. These references should be cleaned up to reflect the new support policy.

## Expected Behavior

- The CI/CD pipeline platform matrix configuration should no longer include LoongArch64 on Linux as a platform target.
- The multi-job variant of the pipeline platform matrix configuration should also have LoongArch64 on Linux removed.
- The Docker workflow documentation should no longer mention LoongArch64 as a supported cross-compilation architecture.

## Why This Matters

Leaving stale platform references in place causes confusion for contributors, misleads automated tooling about which targets are actually tested, and may result in failed or unexpected CI job attempts for a platform that is no longer officially maintained. Removing these entries keeps the project's configuration honest and reduces maintenance burden.
