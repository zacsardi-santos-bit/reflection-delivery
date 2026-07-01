Update the storage migration framework to allow migration implementations to declare applicable storage domains, simplify single account migration, and correct the handling of published values in the inbox domain.

*   Update the `ValueMigration` interface:
    *   Add a `Domains() map[string]struct{}` method to declare applicable storage domains.
    *   Ensure all existing `ValueMigration` implementations implement this method.
    *   If `Domains()` returns `nil`, apply the migration to all storage domains.

*   Modify the migration engine:
    *   Respect the domains declared by `ValueMigration.Domains()`.
    *   Skip non-matching domains when `Domains()` returns a non-nil map of domain identifiers.

*   Implement the `MigrateAccount` method:
    *   Add `MigrateAccount(address common.Address, migrator StorageMapKeyMigrator)` to `*StorageMigration`.
    *   Allow migration of a single account's storage without using an iterator.
    *   Ensure the migrator's `Domains()` return value controls which storage domains are visited.

*   Update `StorageMapKeyMigrator`:
    *   Change from a function type to an interface.
    *   Include `Migrate(...)` and `Domains() map[string]struct{}` methods.
    *   `Domains()` should declare relevant storage domains; `nil` means all domains.

*   Implement `NewValueMigrationsPathMigrator`:
    *   Return a `StorageMapKeyMigrator` with `Domains()` as the union of all provided `ValueMigration.Domains()`.
    *   If any `ValueMigration` returns `nil`, the combined migrator's `Domains()` must also return `nil`.

*   Correct published value handling:
    *   Ensure values in the inbox domain are visited and migrated by `MigrateAccount` when `Domains()` returns `nil`.
    *   Report migration of published values in the inbox domain as successful with no errors.

*   Enforce domain restrictions:
    *   When a migration is restricted via `Domains()`, do not invoke the migrator for non-declared domains.
    *   Ensure the number of migrated entries matches the number of matching stored domains.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.