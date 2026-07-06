I have a monorepo where one workspace package declares a dependency using the aliased package syntax — it uses a local name but explicitly points to a specific npm registry package.

*   Must add an is_npm_alias() method to DependencyVersion in crates/turborepo-repository/src/package_graph/dep_splitter.rs that returns true when the specifier uses npm alias syntax (e.g., "npm:buffer@6.0.3" or "npm:@scope/pkg@^1.0.0") and false for plain npm ranges ("npm:^1.2.3", "npm:*"), non-npm protocols ("workspace:*"), and bare version ranges ("^1.0.0").

*   The is_npm_alias() method must distinguish between an npm alias (has both package name AND version after 'npm:') and a plain npm version range (has only a version after 'npm:'). For scoped packages like "npm:@scope/pkg@^1.0.0", the leading '@' for the scope must not be mistaken for the name/version separator.

*   The dependency resolution logic (is_external or equivalent workspace resolution) must be updated so that any dependency specifier identified as an npm alias is never resolved to a workspace package — even when a workspace package with the same name and version exists. When link_workspace_packages is enabled and a workspace named "buffer" at version 6.0.3 exists, a dependency version of "npm:buffer@6.0.3" must still return no workspace match (None).

*   The Berry lockfile resolver must correctly handle npm alias specifiers. When resolving a dependency named "buffer" with version specifier "npm:buffer@6.0.3" from workspace "packages/a", it must return the npm registry package with key "buffer@npm:6.0.3" and version "6.0.3" — not the workspace package "buffer@workspace:packages/buffer" — even when both entries exist in the lockfile.

*   When pruning a Berry lockfile (subgraph) for workspace "packages/a" with package key "buffer@npm:6.0.3", the resulting encoded lockfile must contain the npm alias entry "buffer@npm:buffer@6.0.3" rather than or in addition to any workspace entry.


*   Interface details: Type: Method
Name: is_npm_alias
Location: crates/turborepo-repository/src/package_graph/dep_splitter.rs
Signature: fn is_npm_alias(&self) -> bool
Description: Private method on the DependencyVersion struct. Returns true if the dependency specifier uses npm alias syntax — i.e., it has the "npm:" protocol AND contains a package name separated from the version by "@". This distinguishes npm aliases (e.g., "npm:buffer@6.0.3", "npm:@scope/pkg@^1.0.0") from plain npm version ranges (e.g., "npm:^1.2.3", "npm:*"). The method is added within the existing impl block for DependencyVersion in dep_splitter.rs. This method must be accessible from the test module in the same file.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.