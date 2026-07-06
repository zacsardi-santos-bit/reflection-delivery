## Description

The codemod migration tool runs automated code transformations too aggressively. Currently, several transforms apply to any file they encounter, regardless of whether that file actually uses the library being migrated. This causes false positives: a configuration file that happens to use the same field names or API patterns as TypeORM gets incorrectly rewritten, even though it has nothing to do with TypeORM.

Additionally, ambient type declaration files are being processed by the tool even though rewriting identifiers inside them would silently corrupt the type definitions that downstream consumers depend on.

## Expected Behavior

- Most transforms should first verify that the file actually imports from the library before making any changes. This check must recognize all common import styles, including standard ES module imports, imports from sub-paths of the package, side-effect imports, CommonJS require calls, and TypeScript-specific import syntax.
- A module whose name merely shares a prefix with the target (e.g. a different package with a similar name) should not count as a match.
- Ambient type declaration files should always be excluded from processing by default, independently of any user-provided exclusion settings.
- User-provided exclusion patterns should be merged with the built-in defaults so that both apply, rather than user patterns replacing the defaults.
- The built-in default exclusion patterns must be accessible as a named export so that callers can inspect or extend them.
- A helper for merging user patterns with the defaults should also be exported so that the merging logic is testable and reusable.

## Why This Matters

Without these guards, running the migration tool on a large codebase risks silently rewriting files that have nothing to do with the ORM — for example, CLI option objects, logger configuration, or third-party library configurations that happen to share key names. It also risks corrupting published type declarations. Both failure modes are difficult to detect and can cause subtle runtime or type-checking breakages after migration.
