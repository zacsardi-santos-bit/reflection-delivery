I'm working on a Rust codebase for a monorepo build tool that parses and manipulates package manager lockfiles.

*   The `subgraph` method must read the lockfile's global settings block and check for an `injectWorkspacePackages` boolean field. When this field is `true`, all workspace dependencies in each included importer must be treated as injected, regardless of whether they also appear in the importer's per-dependency metadata.

*   When an injected dependency (detected either via the global setting or per-dependency metadata) has a resolved version that starts with `file:`, the `subgraph` method must add the corresponding entry to BOTH the `packages` map and the `snapshots` map of the pruned lockfile. Previously, only the `packages` section was populated for per-dependency injection.

*   The `subgraph` method must traverse the transitive dependencies of each injected `file:` package. For each direct dependency listed in the injected package's snapshot, the method must construct the full package key and add the corresponding entries to both `pruned_packages` and `pruned_snapshots`.

*   Injected dependencies whose resolved version does NOT start with `file:` (e.g., those using `link:` resolution) must be skipped — they do not have entries in the lockfile's packages/snapshots sections.

*   When an injected dependency is absent from the importer's dependency resolution (i.e., has no resolved version), it must be silently skipped rather than returning an error.

*   The `LockfileSettings` struct must deserialize an optional `injectWorkspacePackages` boolean field from the `injectWorkspacePackages` YAML key (camelCase). The field must be `None` when the key is absent.

*   The `resolve_package` method must correctly resolve a workspace dependency whose version in the lockfile is `file:<path>` — returning a `Package` whose `key` field is `<package-name>@file:<path>` (e.g., `@repo/shared@file:packages/shared`).

*   After pruning, packages that belong only to workspace importers not included in the requested subset must still be excluded from the pruned lockfile (e.g., a root-only dev dependency like `prettier@3.5.3` must not appear in `packages` when it is only needed by the root importer and not by the requested workspaces).


*   Interface details: Type: Method
Name: subgraph
Location: crates/turborepo-lockfiles/src/pnpm/data.rs
Signature: fn subgraph(&self, workspace_packages: &[String], packages: &[String]) -> Result<Box<dyn crate::Lockfile>, crate::Error>
Description: Creates a pruned version of the lockfile containing only the specified workspace packages and their resolved external dependencies. Must handle injected workspace packages in two ways: (1) when the lockfile's global settings include `injectWorkspacePackages: true`, all workspace dependencies resolved with `file:` protocol must be included in both the `packages` and `snapshots` sections of the pruned lockfile; (2) when individual dependencies have `dependenciesMeta[dep].injected: true`, they must also be included in both sections. In both cases, transitive dependencies of injected packages must be traversed and added.

Type: Struct
Name: LockfileSettings
Location: crates/turborepo-lockfiles/src/pnpm/data.rs
Description: Represents the settings block in a pnpm lockfile (v9). Must include an `inject_workspace_packages: Option<bool>` field (serialized as `injectWorkspacePackages` in YAML via `#[serde(rename_all = "camelCase")]`) that, when `true`, causes all workspace dependencies to be treated as injected.

Type: Struct
Name: DependenciesMeta
Location: crates/turborepo-lockfiles/src/pnpm/data.rs
Description: Represents per-dependency metadata in the `dependenciesMeta` section of a pnpm importer. Must include an `injected: Option<bool>` field. When `injected` is `true` and the dependency resolves to a `file:` version, the dependency must be treated as an injected workspace package and included in both packages and snapshots of the pruned lockfile.

Type: Function (test)
Name: test_subgraph_with_injected_workspace_packages_setting
Location: crates/turborepo-lockfiles/src/pnpm/data.rs (inside the `tests` module)
Signature: fn test_subgraph_with_injected_workspace_packages_setting()
Description: Tests that `subgraph` correctly handles the global `injectWorkspacePackages: true` lockfile setting (pnpm 10 style). Uses a lockfile with `settings.injectWorkspacePackages: true` where `apps/my-app` depends on `@repo/shared` resolved to `file:packages/shared`. Verifies that after calling `subgraph(["apps/my-app", "packages/shared"], ["is-number@6.0.0", "is-odd@3.0.1", "lodash@4.17.21"])`: the key `@repo/shared@file:packages/shared` is present in both `packages` and `snapshots`; `is-odd@3.0.1`, `is-number@6.0.0`, and `lodash@4.17.21` appear in both sections; and `prettier@3.5.3` does NOT appear in `packages`.

Type: Function (test)
Name: test_subgraph_with_per_dep_injected_meta_and_file_version
Location: crates/turborepo-lockfiles/src/pnpm/data.rs (inside the `tests` module)
Signature: fn test_subgraph_with_per_dep_injected_meta_and_file_version()
Description: Tests that `subgraph` correctly handles the per-dependency `dependenciesMeta.injected: true` case with `file:` resolution (pnpm 9 style). Uses a lockfile without global injection where `apps/web` has `@repo/ui` resolved to `file:packages/ui` with `dependenciesMeta["@repo/ui"].injected: true`. Verifies that after calling `subgraph(["apps/web", "packages/ui"], [])` (empty resolved packages list): the key `@repo/ui@file:packages/ui` is present in both `packages` and `snapshots`; and `is-odd@3.0.1` appears in both `packages` and `snapshots` (discovered via transitive dependency traversal of the injected package).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.