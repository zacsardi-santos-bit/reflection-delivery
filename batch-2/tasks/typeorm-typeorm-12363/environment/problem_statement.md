# Make the Expo SQLite driver field optional and add auto-loading with validation

## Description

When setting up a data source for Expo SQLite in TypeORM, users have always had to pass the SQLite library module explicitly in the configuration. With the latest version of TypeORM this manual injection is completely unnecessary — the framework can load the library on its own. The field should become optional, and when omitted, TypeORM should automatically load the appropriate SQLite library.

Beyond making the field optional, the driver should also validate that whatever SQLite module is in use — whether supplied explicitly or auto-loaded — is a sufficiently modern version. Older versions of the Expo SDK exposed only a legacy synchronous API; the current driver only supports the newer asynchronous API. When the wrong version is detected, users should receive a clear error message pointing them toward the required upgrade. When the library is not installed at all, a clear installation error should be shown. Errors from other missing dependencies (transitive requirements of the library) should be propagated as-is so they are not misattributed to a missing Expo SQLite installation.

## Expected Behavior

- The driver configuration field for the SQLite module should be optional.
- If no module is provided, TypeORM loads it automatically.
- If an explicitly provided module lacks the modern async API, a descriptive error mentioning the SDK upgrade requirement is thrown.
- If the auto-loaded module lacks the modern async API, a similar upgrade error is thrown.
- If the library is not installed, a package-not-installed error mentioning the library name is thrown.
- If a transitive dependency of the library is missing, the original error is re-thrown unchanged.
- Non-installation errors from loading the module are re-thrown unchanged.

## Migration Tooling

A codemod transform should be provided to automatically clean up existing codebases. It should remove the now-redundant explicit driver injection from Expo data source configurations, but only when the driver value can be confidently identified as the default library import. It must leave more complex patterns untouched: member-access extractions, variable indirections, custom or patched packages, and configurations that cannot be unambiguously recognized as Expo data sources. The transform must also correctly handle configurations using quoted property keys, exported defaults, spread/factory patterns, and cases where the driver property is the only extra field in the object.

## Why This Matters

Requiring users to manually inject a library that TypeORM can load automatically is unnecessary boilerplate and a common stumbling block for Expo users. Making it optional reduces setup friction and ensures users receive helpful guidance if their installed version is incompatible.
