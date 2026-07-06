I'm working with a monorepo that uses a newer version of a popular package manager that generates a multi-document lockfile.

*   When a pnpm lockfile is in multi-document YAML format (i.e., contains multiple documents separated by YAML document delimiters), the lockfile parser must detect this format and use only the workspace dependency document for building the dependency graph, not the package-manager metadata document.

*   In a multi-document lockfile where the first document contains package-manager dependency metadata (such as the package manager's own version entry), that first document's packages must NOT appear as nodes in the returned graph. Specifically, a package listed only under packageManagerDependencies in the first document should be absent from the parsed nodes.

*   Regular workspace dependencies (e.g., ordinary npm packages declared in the workspace importers of the main lockfile document) must still be parsed correctly and appear in the returned nodes with the expected packageName, version, hash, name, and type fields, even when the lockfile uses the multi-document format.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.