# Add Transaction Isolation Level Support

## Description

Currently, when starting a database transaction in TypeORM, there is no way to specify the isolation level. All transactions run at the database's default isolation level, which means developers have no control over concurrency behaviors such as dirty reads, non-repeatable reads, or phantom reads on a per-transaction basis.

## Expected Behavior

- It should be possible to pass an isolation level when executing a transaction callback, so that the transaction starts at the specified level.
- Supported isolation levels should include read-uncommitted, read-committed, repeatable-read, and serializable modes.
- Each database driver should handle isolation levels according to its own capabilities. For example, SQLite only supports two of the four standard levels and should apply the correct internal configuration before beginning the transaction.
- The transaction method decorator should also support specifying an isolation level through an options object, alongside the existing connection name configuration.
- When an isolation level is specified, entities saved within the transaction should be committed and retrievable just like transactions without an isolation level.

## Why This Matters

Without this capability, teams that need stronger or weaker isolation guarantees for specific operations (such as long-running reports, batch updates, or sensitive financial transactions) cannot configure this through TypeORM. Developers are forced to write raw SQL to work around this limitation.
