## Description

When using workspace injection in a monorepo managed by a package manager that supports copying workspace packages into node_modules as real installed packages (rather than symlinks), the pruning feature that creates minimal lockfile subsets for individual apps fails to handle these injected packages correctly.

There are two broken scenarios:

1. **Global injection setting (newer-style configuration):** Some package manager versions support a global workspace-level setting that enables injection for all workspace packages. When this setting is active, workspace dependencies are resolved using a file-based protocol rather than the typical symlink/link approach, which causes them to appear in the lockfile's packages and snapshots sections. However, the pruning logic does not recognize this global setting, so injected workspace packages are silently dropped from the pruned lockfile — making the subset incomplete and non-installable.

2. **Per-dependency injection with file resolution:** Even in older setups where injection is declared individually per-dependency, a bug causes the injected package to be added to the packages section of the pruned lockfile but omitted from the snapshots section. Additionally, the transitive dependencies of the injected package are not traversed or included in the pruned lockfile.

## Expected Behavior

- When the lockfile has a global injection setting enabled, all workspace dependencies resolved via the file protocol should appear in both the packages and snapshots sections of the pruned lockfile.
- When individual dependencies are marked as injected with file-based resolution, they must appear in both the packages and snapshots sections of the pruned lockfile.
- In both cases, the transitive dependencies of each injected workspace package must be discovered and included in the pruned lockfile.
- Packages that belong only to workspaces not included in the subset must still be excluded from the pruned lockfile.

## Why This Matters

Users running pruning to create deployable subsets of their monorepo will get incomplete lockfiles that cannot be installed correctly, breaking CI/CD pipelines and Docker builds that rely on the pruned output.
