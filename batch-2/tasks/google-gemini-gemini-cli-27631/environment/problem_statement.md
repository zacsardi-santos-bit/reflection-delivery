## Description

The project's evaluation test system has grown to the point where there's a need for tooling that can work with eval test cases programmatically — indexing them, filtering by policy, generating reports, or validating their structure. However, no utility currently exists to extract structured information from eval files without executing them.

Eval files follow several different patterns: some use direct imports of known helpers, some use aliased imports, some define local wrapper functions (at top level or inside describe blocks, as regular functions or arrow function variables), and some files use JSX/TSX syntax. Any static analysis utility needs to handle all of these patterns correctly.

## Expected Behavior

- Given the source text of an eval file, the utility should extract all statically-defined test cases and return structured metadata for each: the policy, test name, suite name, suite type, timeout, and whether a prompt and files are configured.
- The utility should resolve wrapper helpers to their original base helper, tracking the mapping for every alias or local wrapper encountered.
- When a test case uses dynamic (runtime) values for its policy or case object, the utility should report a diagnostic message explaining what it could not resolve, rather than silently skipping or crashing.
- File paths should always be returned with forward slashes regardless of the host operating system's path separator convention.
- The utility should correctly parse files that include component/JSX syntax.

## Why This Matters

Without this utility, any tooling that needs to work with eval test cases must either run them (slow, requires environment setup) or implement its own ad-hoc parsing. A single shared analysis utility makes it possible to build reliable, consistent tooling on top of the eval infrastructure.
