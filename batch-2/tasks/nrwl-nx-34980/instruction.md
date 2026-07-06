I'm working on improving error handling in the migration system.

*   When Migrator.migrate() fetches migration metadata for a package and the returned configuration object has no version property, the method must immediately throw an error with the message: 'Fetched migration metadata for <packageName> is invalid: the target version is missing.'

*   When Migrator.migrate() processes a parent package whose fetched migration metadata includes a packageJsonUpdates entry that references a child package with an undefined version, AND the update group would be applied (its requirements are satisfied), the method must throw an error with the message: 'Fetched migration metadata for <parentPackage> is invalid: the target version for <childPackage> is missing.'

*   When Migrator.migrate() encounters a packageJsonUpdates entry with a child package missing a version, but the update group is skipped because its 'requires' conditions are not met, the method must NOT throw. It must return normally with only the top-level package update included in packageUpdates and an empty migrations array.

*   A new exported function formatCommandFailure(command, error) must be added to the migrate module. When the error object has a non-empty stderr property, the function must return the error message and stderr joined by a newline. When the error object has no stderr property, the function must return the error message as-is (which may itself contain embedded newlines).


*   Interface details: Type: Function
Name: formatCommandFailure
Location: packages/nx/src/command-line/migrate/migrate.ts
Signature: formatCommandFailure(command: string, error: { message: string; stderr?: string }): string
Description: Formats a child process command failure for display. When the error has a stderr property, returns the error message and stderr joined with a newline. When stderr is absent, returns the error message as-is. Must be exported from the module.

Type: Class
Name: Migrator
Location: packages/nx/src/command-line/migrate/migrate.ts
Description: The existing Migrator class must be updated so that its migrate() method validates fetched migration metadata. Specifically: (1) if the top-level fetched config for the target package is missing a version property, migrate() must throw with message 'Fetched migration metadata for <packageName> is invalid: the target version is missing.'; (2) if a packageJsonUpdates entry for a child package is missing a version and that update group is being applied, migrate() must throw with message 'Fetched migration metadata for <parentPackage> is invalid: the target version for <childPackage> is missing.'; (3) if the problematic packageJsonUpdates entry belongs to a group that is skipped (due to unmet requires conditions), migrate() must not throw.
Signature: migrate(targetPackage: string, targetVersion: string): Promise<{ migrations: any[]; packageUpdates: Record<string, { version: string; addToPackageJson: boolean }> }>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.