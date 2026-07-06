Implement support for specifying transaction isolation levels in TypeORM. Update the transaction methods to accept isolation levels and ensure compatibility with different database drivers, including SQLite. Enhance the @Transaction decorator to accept an options object with isolation level and connection name.

*   Define the `IsolationLevel` type as a union of string literals: "READ UNCOMMITTED", "READ COMMITTED", "REPEATABLE READ", "SERIALIZABLE".
    *   File: `src/driver/types/IsolationLevel.ts`
    *   Signature: `export type IsolationLevel = "READ UNCOMMITTED" | "READ COMMITTED" | "REPEATABLE READ" | "SERIALIZABLE";`

*   Update the `EntityManager.transaction()` method to support an overloaded signature:
    *   Accepts an optional `IsolationLevel` string as the first argument followed by the callback.
    *   Signature: 
        ```typescript
        transaction<T>(runInTransaction: (entityManager: EntityManager) => Promise<T>): Promise<T>
        transaction<T>(isolationLevel: IsolationLevel, runInTransaction: (entityManager: EntityManager) => Promise<T>): Promise<T>
        ```
    *   Forward the isolation level to the `QueryRunner`'s `startTransaction` method.

*   Modify the `QueryRunner` interface's `startTransaction()` method to accept an optional `IsolationLevel` parameter.
    *   Signature: `startTransaction(isolationLevel?: IsolationLevel): Promise<void>`

*   Implement the `startTransaction()` method in `AbstractSqliteQueryRunner` to handle SQLite-specific behavior:
    *   Execute `PRAGMA read_uncommitted = true` for "READ UNCOMMITTED".
    *   Execute `PRAGMA read_uncommitted = false` for "SERIALIZABLE".
    *   Throw an error for unsupported isolation levels.

*   Ensure that all entities saved within a transaction with a specified isolation level are committed and retrievable after the transaction completes.

*   Update the `@Transaction` decorator to accept either a connection name string or a `TransactionOptions` object.
    *   Define `TransactionOptions` interface with optional `connectionName` and `isolationLevel` properties.
    *   File: `src/decorator/options/TransactionOptions.ts`
    *   Signature: 
        ```typescript
        interface TransactionOptions {
            connectionName?: string;
            isolationLevel?: IsolationLevel;
        }
        ```
    *   Signature for `Transaction` decorator:
        ```typescript
        Transaction(connectionName?: string): MethodDecorator
        Transaction(options?: TransactionOptions): MethodDecorator
        ```

*   Add a `saveWithNonDefaultIsolation()` method to `PostController` in the transaction-decorator test:
    *   Decorate with `@Transaction({ connectionName: "mysql", isolationLevel: "SERIALIZABLE" })`.
    *   Accept a `Post`, a `Category`, and an injected `EntityManager`.
    *   Save both entities using the injected entity manager.
    *   File: `test/functional/transaction/transaction-decorator/controller/PostController.ts`
    *   Signature: 
        ```typescript
        saveWithNonDefaultIsolation(post: Post, category: Category, @TransactionManager() entityManager: EntityManager): Promise<void>
        ```

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.