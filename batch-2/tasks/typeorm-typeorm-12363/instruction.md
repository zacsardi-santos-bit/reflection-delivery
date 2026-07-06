I'm working on TypeORM's Expo SQLite driver and need to make the SQLite library field in the data source configuration optional, so users no longer have to pass it manually.

*   The ExpoDriver class must expose a protected `loadDependencies()` method and a protected `requireExpoSqlite()` method. The `sqlite` property on the driver instance must be assigned inside `loadDependencies()`.

*   When `options.driver` is explicitly provided and it exposes `openDatabaseAsync`, `loadDependencies()` must assign it to `this.sqlite` without error.

*   When `options.driver` is explicitly provided but does NOT expose `openDatabaseAsync` (e.g., only has `openDatabase`), `loadDependencies()` must throw a `TypeORMError` whose message matches the pattern /custom overrides must match/.

*   When `options.driver` is `undefined`, `loadDependencies()` must call `requireExpoSqlite()` to obtain the module. If the resolved module exposes `openDatabaseAsync`, it must be assigned to `this.sqlite`.

*   When `options.driver` is `undefined` and `requireExpoSqlite()` returns a module that lacks `openDatabaseAsync` (stale SDK), `loadDependencies()` must throw a `TypeORMError` whose message matches the pattern /Expo SDK v52 or later/.

*   When `options.driver` is `undefined` and `requireExpoSqlite()` throws an error whose `code` is `MODULE_NOT_FOUND` and whose first message line identifies `expo-sqlite` as the missing module, `loadDependencies()` must throw a `DriverPackageNotInstalledError` whose message includes the string `expo-sqlite`.

*   When `requireExpoSqlite()` throws a `MODULE_NOT_FOUND` error but the first line of the error message names a module other than `expo-sqlite` (i.e., a transitive dependency), `loadDependencies()` must re-throw the original error unchanged rather than wrapping it in `DriverPackageNotInstalledError`.

*   When `requireExpoSqlite()` throws any error that is NOT `MODULE_NOT_FOUND`, `loadDependencies()` must re-throw it unchanged.

*   The `driver` field on `ExpoDataSourceOptions` must be optional (not required), so that a data source can be configured without explicitly supplying the SQLite module.

*   A codemod transform must be present at `packages/codemod/src/transforms/v1/datasource-expo.ts`. When applied, it must remove any `driver` property whose value is exactly `require("expo-sqlite")` (a plain call expression, not a member access or indirect reference) from object expressions that contain BOTH a `type: "expo"` property AND a `database` property.

*   The codemod must NOT remove `driver` when its value is a member access such as `require("expo-sqlite").default`, an identifier/variable, or a call to a package other than `expo-sqlite`.

*   The codemod must NOT touch objects that have `type: "expo"` but lack a `database` property, to avoid false positives on unrelated configuration objects.

*   The codemod must correctly handle objects where property keys are string literals (e.g. `"driver"`, `"type"`, `"database"`), exported default DataSource configs, objects where the driver is the last property (no trailing-comma artifact), and plain object literals used as spread sources (factory pattern).


*   Interface details: Type: Class
Name: ExpoDriver
Location: src/driver/expo/ExpoDriver.ts
Description: The Expo SQLite driver class. Must expose a protected `loadDependencies()` method that either uses an explicitly provided driver from `this.options.driver` or auto-loads `expo-sqlite` via `requireExpoSqlite()`. Must expose a protected `requireExpoSqlite()` method that can be overridden by subclasses to control module resolution. The loaded module must be assigned to the public `sqlite` property. Validation errors, installation errors, and re-thrown errors are described in requirements.
Signature:
  protected loadDependencies(): void
  protected requireExpoSqlite(): unknown
  public sqlite: unknown

Type: Interface/Type
Name: ExpoDataSourceOptions
Location: src/driver/expo/ExpoDataSourceOptions.ts
Description: Data source options for the Expo SQLite driver. The `driver` field must be optional — omitting it causes TypeORM to auto-load the SQLite module.
Signature:
  driver?: any   // optional; omit to let TypeORM auto-load expo-sqlite

Type: Function (jscodeshift transform)
Name: datasourceExpo (default export)
Location: packages/codemod/src/transforms/v1/datasource-expo.ts
Description: A jscodeshift codemod transform that removes redundant `driver: require("expo-sqlite")` properties from Expo data source configuration objects. The transform's default export must follow the jscodeshift transform signature.
Signature: (file: FileInfo, api: API) => string


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.