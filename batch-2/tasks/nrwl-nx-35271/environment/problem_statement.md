## Description

A newer major version of a popular JavaScript package manager introduced a multi-document lockfile format. When a certain version-management feature is enabled, the lockfile file contains two separate YAML documents: the first document records metadata about the package manager tool itself (its own version information), and the second document holds the actual workspace dependency tree for the project.

The existing lockfile parser does not account for this format. As a result, it processes the wrong document or incorrectly surfaces the package manager itself as a project dependency, while the real workspace dependencies may be missing or unreachable.

## Expected Behavior

- When a lockfile is in the multi-document format, the parser should detect this and automatically select the workspace dependency document for analysis.
- Packages listed only in the package-manager metadata section of the first document should NOT appear as dependency nodes in the graph.
- All regular project dependencies declared in the workspace document should be correctly parsed and included in the dependency graph with accurate name, version, and hash information.

## Why This Matters

Projects upgrading to this newer package manager version with version-management enabled will produce an incorrect or broken dependency graph. Fixing the parser to correctly handle the multi-document format restores compatibility and ensures the dependency graph reflects the true set of project dependencies.
