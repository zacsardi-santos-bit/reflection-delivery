I'm working on the TypeORM codemod package and need to add a new automated migration transform for a breaking change in TypeORM v1.

*   The file-logger transform must be implemented at packages/codemod/src/transforms/v1/file-logger.ts and exported as both `fn` and the default export, following the standard jscodeshift transform signature: (file: FileInfo, api: API) => string | undefined. It must return the modified source string when changes were made, or undefined when no changes are needed.

*   The transform must be registered in packages/codemod/src/transforms/v1/index.ts so it is included in the v1 transforms list.

*   When the transform inserts a warning, it must insert exactly this comment on the line immediately before the flagged statement: `// TODO(typeorm-v1): \`FileLogger\` now resolves \`logPath\` from \`process.cwd()\` instead of the app root — use an absolute path if the app is not started from its root folder`

*   The transform must track the file logger class from the 'typeorm' package regardless of import style: direct named ESM import, aliased ESM import, namespace ESM import (import * as typeorm), CommonJS destructured require, CommonJS aliased destructured require, and CommonJS whole-module require (const typeorm = require('typeorm')).

*   The transform must NOT modify files where the file logger class is imported from any package other than 'typeorm'.

*   The transform must insert the TODO comment before an instantiation in these cases: no options argument (default log path); options argument of undefined or null; inline object options with no logPath property; inline object options where logPath is undefined or null; inline object options where logPath is a relative string literal (not starting with '/' or a UNC path prefix); inline object options that combine a spread with an explicit relative string literal logPath.

*   The transform must NOT insert the TODO comment in these cases: inline object options where logPath is an absolute string literal (starts with '/' or '\\\\'); inline object options that contain only spreads (logPath cannot be statically determined); a non-literal expression as the logPath value (e.g., a path.resolve() call); a dynamic variable (non-object-literal) passed as the entire options argument.

*   When multiple instantiations of the file logger appear within a single statement, only ONE TODO comment must be inserted before that statement — not one per instantiation.

*   The TODO comment must be placed before the enclosing statement node (VariableDeclaration, ExpressionStatement, ReturnStatement, ExportDefaultDeclaration, ClassProperty/PropertyDefinition, etc.), not inline with the new expression.

*   The test runner supports multiple fixture pairs per transform directory. Each fixture pair consists of a `<name>.input.ts` file and a `<name>.output.ts` file inside packages/codemod/test/transforms/v1/fixtures/file-logger/. When the transform returns undefined (no changes), the result is treated as the original input unchanged.


*   Interface details: Type: Module
Name: file-logger
Location: packages/codemod/src/transforms/v1/file-logger.ts
Description: A jscodeshift codemod transform that scans TypeScript/JavaScript source files and inserts a TODO warning comment before instantiations of the file logger class from "typeorm" that use a non-absolute log path (or omit the log path entirely). The module must export the transform function as either `fn` or as the default export so that the test runner can invoke it via jscodeshift's `applyTransform`. The transform must also be registered in the v1 transforms index at `packages/codemod/src/transforms/v1/index.ts`.

Signature (transform function):
  (file: FileInfo, api: API) => string | undefined
  Returns the transformed source string when changes were made, or `undefined` when no changes are needed.

Required exports:
  - `fn` — the transform function (used by the test runner as `transformModule.fn`)
  - `default` — also the transform function (fallback)

The exact TODO comment text that must be inserted is:
  `// TODO(typeorm-v1): \`FileLogger\` now resolves \`logPath\` from \`process.cwd()\` instead of the app root — use an absolute path if the app is not started from its root folder`

The transform is keyed by name `"file-logger"` (derived from the filename without extension). The test runner resolves the transform file as `src/transforms/v1/file-logger.ts` based on the fixture directory name.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.