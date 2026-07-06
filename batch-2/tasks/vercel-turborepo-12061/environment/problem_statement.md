## Description

In a monorepo, when a package declares a dependency using aliased package syntax to reference a specific registry package under a local name, and a local workspace package with that same name also exists, the build system incorrectly treats the dependency as a reference to the local workspace package. The user's explicit intent — to use the npm registry version — is silently ignored.

This bug affects both the high-level dependency splitting logic (which decides whether a dependency is internal or external to the workspace) and the low-level lockfile parsing (which resolves the exact package to use).

## Expected Behavior

- When a dependency uses the alias syntax to explicitly request a registry package, it must never be resolved to a workspace package, even if a workspace with a matching name and version exists.
- The distinction between a plain registry version range (which may still resolve to a workspace) and an aliased registry reference (which must always be treated as external) should be correctly identified and enforced.
- When pruning a lockfile for a workspace that uses aliased dependencies, the pruned lockfile should include the registry version of the aliased package, not the workspace version.

## Why This Matters

This causes incorrect build graphs in monorepos where workspace packages share a name with npm packages. Developers explicitly using the aliasing syntax to opt into the npm registry version end up unknowingly using the local workspace package, which can lead to subtle version mismatches and broken builds that are difficult to diagnose.

Related issue: https://github.com/vercel/turborepo/issues/8989
