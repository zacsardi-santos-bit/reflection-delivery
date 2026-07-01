## Description

The wallet client module needs a way to persistently record whether the connected federation supports processing all Bitcoin deposits, including large on-chain transactions that exceed the consensus unit size limit. Currently there is no database entry to track this capability, so a client cannot remember — without going online — whether the federation has been verified to support this feature.

## Expected Behavior

- A new database key prefix should be introduced in the wallet client to represent the stored capability flag for safe deposit support.
- The database migration test, which exhaustively matches every known key prefix to ensure all are accounted for, should be updated to include this new prefix so the test continues to compile and pass.

## Why This Matters

Without this capability flag in the database, a client must always go online to check whether the federation supports all deposit types. By persisting this information, the client can answer this question offline once it has successfully verified it at least once. The migration test coverage ensures that existing client databases can be safely upgraded as new key prefixes are added.
