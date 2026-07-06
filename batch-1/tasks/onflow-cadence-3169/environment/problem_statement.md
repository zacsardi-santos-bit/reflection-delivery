## Description

The storage migration framework currently requires wrapping every address in an iterator object even when migrating a single account, which is unnecessarily verbose. More importantly, individual migration implementations have no way to declare which storage domains they care about — every migration currently scans all storage domains even if it is only relevant to one or two of them. We also have a bug where published values stored in the inbox domain are not correctly processed when the value inside is a certain kind of capability, causing panics or silent failures.

## Expected Behavior

- Individual migration implementations should be able to declare a set of storage domains they apply to. When a migration declares specific domains, the framework should skip all other domains entirely rather than running the migration and letting it silently do nothing.
- If a migration declares no domain restrictions, it should continue to apply to all domains as before, including the inbox domain.
- Migrating a single account should be possible without needing to construct and pass an iterator object — the address should be passable directly.
- Published values containing capability values stored in the inbox domain should be correctly visited and processed during migration with no errors.

## Why This Matters

Migrations that only apply to a subset of storage domains (e.g., only the public and private path domains) are currently forced to iterate over all domains, wasting time and risking accidental side effects. By letting each migration declare its relevant domains, the framework becomes more efficient and migrations become self-documenting. Fixing the published-value migration issue ensures data integrity for accounts that have published capabilities.
