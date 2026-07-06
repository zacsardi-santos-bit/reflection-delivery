## Description

The Remote Config SDK's condition evaluation feature relies on a third-party hashing library to determine which percentage of users match targeting conditions. The library currently in use has compatibility issues with modern Node.js versions and should be replaced with a newer alternative that provides better support going forward.

## Expected Behavior

- The hashing library used internally for Remote Config percent-condition evaluation should be updated to the modern replacement package.
- The updated library returns hash values as native 64-bit integers rather than strings. The implementation must handle this type difference and convert the hash value appropriately before performing numeric comparisons.
- All existing percent-based targeting conditions (less-or-equal, greater-than, and between operators) should continue to produce correct results after the migration.
- Tests that exercise the actual hashing function on older Node.js runtime versions should be skipped gracefully, since the newer library does not support those versions.

## Why This Matters

The old hashing library is not fully compatible with current and future Node.js versions. Migrating to the modern alternative keeps the SDK functional and maintainable without changing any observable user-targeting behavior.
