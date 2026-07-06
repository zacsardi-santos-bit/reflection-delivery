I'm using the TypeORM migration codemod to help update our codebase to the new TypeORM API, but it's missing several patterns that we rely on heavily.

*   The connection-options-reader transform must rename the `.all()` method call to `.get()` on ConnectionOptionsReader bindings even when the binding is declared with `let` in an outer scope and assigned inside a nested function scope (i.e., the declaration scope differs from the assignment-site scope).

*   The connection-options-reader transform must recognize computed string member access such as `reader["all"]()` and optional-chained `reader?.["all"]()` as equivalent to `reader.all()`, and must rename them to `reader.get()` and `reader?.get()` respectively (converting from bracket notation to dot notation in the output).

*   The connection-to-datasource transform must rename deprecated symbol names in export specifiers sourced from "typeorm" — for example `export { Connection, ConnectionOptions } from "typeorm"` must become `export { DataSource, DataSourceOptions } from "typeorm"`.

*   When renaming an aliased re-export such as `export { Connection as DbConnection } from "typeorm"`, only the local/imported specifier name is renamed (e.g. to `DataSource`); the exported alias (e.g. `DbConnection`) must be preserved unchanged in the output.

*   Sub-path re-exports such as `export { SapConnectionOptions } from "typeorm/driver/sap/SapConnectionOptions"` must have both the specifier name and the module source path rewritten to their v1 equivalents (e.g. `export { SapDataSourceOptions } from "typeorm/driver/sap/SapDataSourceOptions"`).

*   The global-functions transform must remove deprecated removed-global names (such as `getRepository` and `createConnection`) from barrel re-export statements sourced from "typeorm", while preserving non-deprecated exported names (such as `DataSource` and `EntityManager`) in the same statement. If all specifiers in an export statement are removed, the entire declaration must be removed.

*   The mongodb-types transform must redirect `export { ObjectId } from "typeorm"` re-exports to `export { ObjectId } from "mongodb"`, removing the specifier from the typeorm source.

*   When redirecting an ObjectId re-export to mongodb, if a non-type-only `export { ... } from "mongodb"` already exists in the same file, the specifier must be merged into that existing declaration rather than creating a duplicate export declaration.

*   When redirecting an ObjectId re-export to mongodb, a `export type { ... } from "mongodb"` declaration (type-only export) must NOT be used for merging, because ObjectId is a runtime value. A fresh value export declaration must be created instead.

*   The repository-abstract transform must add TODO migration comments to usages of `@EntityRepository`, `extends AbstractRepository`, and `getCustomRepository()` that are accessed via ESM namespace imports (e.g. `import * as typeorm from "typeorm"` followed by `@typeorm.EntityRepository`, `typeorm.AbstractRepository`, `typeorm.getCustomRepository`).

*   The repository-abstract transform must also add the same TODO migration comments when the namespace is bound via a TypeScript `import ns = require("typeorm")` declaration and the deprecated symbols are accessed as `ns.EntityRepository`, `ns.AbstractRepository`, and `ns.getCustomRepository()`.


*   Interface details: The tests in this task are fixture-based: the test harness maps each fixture directory name to the corresponding transform module by convention, applies the transform to the `*.input.ts` file, and asserts that the result matches the `*.output.ts` file. No new top-level functions need to be exported with a specific name for the tests to pass; correctness is verified entirely through the transform output.

The following existing transform modules must be modified to handle the new fixture cases:

Type: Function
Name: connectionOptionsReader
Location: packages/codemod/src/transforms/v1/connection-options-reader.ts
Signature: connectionOptionsReader(file: FileInfo, api: API) => string | undefined
Description: Transforms ConnectionOptionsReader usage. Must be extended to (a) track bindings across scope boundaries (declared in outer scope, assigned in nested scope) and (b) recognise computed string member access `["all"]()` / `?.["all"]()` in addition to dot-notation `.all()`, renaming all forms to `.get()` in dot-notation.

Type: Function
Name: connectionToDataSource
Location: packages/codemod/src/transforms/v1/connection-to-datasource.ts
Signature: connectionToDataSource(file: FileInfo, api: API) => string | undefined
Description: Transforms Connection→DataSource usages. Must be extended to rename deprecated symbol names (e.g. Connection→DataSource, ConnectionOptions→DataSourceOptions) in export specifiers sourced from "typeorm" or typeorm sub-paths, rewriting both specifier names and source module paths as needed. For aliased re-exports, only the local specifier name is renamed; the exported alias is preserved.

Type: Function
Name: globalFunctions
Location: packages/codemod/src/transforms/v1/global-functions.ts
Signature: globalFunctions(file: FileInfo, api: API) => string | undefined
Description: Flags/removes deprecated global functions. Must be extended to strip removed-global names from barrel re-export declarations sourced from "typeorm", leaving non-deprecated specifiers intact.

Type: Function
Name: mongodbTypes
Location: packages/codemod/src/transforms/v1/mongodb-types.ts
Signature: mongodbTypes(file: FileInfo, api: API) => string | undefined
Description: Redirects ObjectId from typeorm to mongodb. Must be extended to handle `export { ObjectId } from "typeorm"` re-exports: remove the specifier from the typeorm export and either merge it into an existing value-only `export { ... } from "mongodb"` declaration or create a new one. Must NOT merge into a type-only `export type { ... } from "mongodb"` declaration.

Type: Function
Name: repositoryAbstract
Location: packages/codemod/src/transforms/v1/repository-abstract.ts
Signature: repositoryAbstract(file: FileInfo, api: API) => string | undefined
Description: Flags removed AbstractRepository / EntityRepository / getCustomRepository APIs. Must be extended to detect and flag usages accessed via ESM namespace imports (`import * as ns from "typeorm"`) and TypeScript require-style namespace bindings (`import ns = require("typeorm")`), adding the same TODO migration comments as for directly named imports.

---

The shared AST helper module may also need new utility functions to support the above transforms. These helpers do not need to match any specific exported name — they are internal implementation details not called directly by the tests:

Type: Module
Name: ast-helpers
Location: packages/codemod/src/transforms/ast-helpers.ts
Description: Shared AST utilities. The transforms above need support for: (1) collecting namespace binding names for a module (covering ESM namespace imports and TS import-equals-require), (2) removing named specifiers from export declarations (including sub-path sources), and (3) renaming named specifiers in export declarations while preserving exported aliases. These may be added as new exported functions or inline logic within each transform.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.