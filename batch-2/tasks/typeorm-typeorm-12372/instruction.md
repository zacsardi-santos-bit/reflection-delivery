I'm working on a codemod tool that automates migration of TypeORM codebases, and I've noticed that several of its transforms are running on files that don't actually import from TypeORM at all.

*   DEFAULT_IGNORE_PATTERNS must be an exported constant (string array) from packages/codemod/src/cli/run-transforms.ts and must include the glob pattern "**/*.d.ts".

*   buildIgnorePatterns must be exported from packages/codemod/src/cli/run-transforms.ts and must return an array deep equal to DEFAULT_IGNORE_PATTERNS when called with no arguments or with undefined.

*   When buildIgnorePatterns is called with a non-empty string array, it must return a new array containing all DEFAULT_IGNORE_PATTERNS entries first, followed by the user-supplied patterns in order.

*   buildIgnorePatterns must not mutate DEFAULT_IGNORE_PATTERNS: after calling buildIgnorePatterns with user patterns, DEFAULT_IGNORE_PATTERNS must be unchanged.

*   fileImportsFrom must return true for ESM imports (named, type, namespace, and side-effect) from the exact module name.

*   fileImportsFrom must return true for ESM imports from sub-paths of the module (e.g. "moduleName/driver/sub/path").

*   fileImportsFrom must return true for TypeScript import-equals-require syntax (import x = require("moduleName")) using the exact module name.

*   fileImportsFrom must return true for CommonJS require() calls using the exact module name or any sub-path of it.

*   fileImportsFrom must return false when the file only imports from a module whose name starts with the given module name but is not an exact match or sub-path (e.g. "moduleName-extension" must not match for "moduleName").

*   fileImportsFrom must return false when the file contains no import declarations or require() calls.

*   fileImportsFrom must return false when require() calls in the file are for a different module.


*   Interface details: Type: Constant
Name: DEFAULT_IGNORE_PATTERNS
Location: packages/codemod/src/cli/run-transforms.ts
Signature: DEFAULT_IGNORE_PATTERNS: string[]
Description: Exported constant array of glob patterns that are always excluded from codemod transforms. Must include "**/*.d.ts".

Type: Function
Name: buildIgnorePatterns
Location: packages/codemod/src/cli/run-transforms.ts
Signature: buildIgnorePatterns(userPatterns?: string[]): string[]
Description: Merges user-supplied ignore patterns with DEFAULT_IGNORE_PATTERNS. When called with no arguments or undefined, returns an array deep equal to DEFAULT_IGNORE_PATTERNS. When called with a string array, returns a new array with DEFAULT_IGNORE_PATTERNS items first, followed by the user-supplied patterns. Must not mutate DEFAULT_IGNORE_PATTERNS.

Type: Function
Name: fileImportsFrom
Location: packages/codemod/src/transforms/ast-helpers.ts
Signature: fileImportsFrom(root: Collection, j: JSCodeshift, moduleName: string): boolean
Description: Checks whether a source file contains an import from the given module. Must return true for: ESM named/type/side-effect imports from the exact module name; ESM imports from sub-paths (e.g. "moduleName/subpath"); TypeScript import-equals-require syntax; CommonJS require() calls using the exact module name or a sub-path. Must return false if the only match is a module that merely shares a name prefix (e.g. "moduleName-extension"), if the file contains no import or require at all, or if require() calls target a different module.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.