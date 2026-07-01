Ensure that the ORM's lazy database connection initialization handles concurrent transactions without errors. Implement a mechanism that allows only one physical connection initialization when multiple transactions start concurrently.

*   Modify the ORM to handle concurrent transactions safely during lazy initialization.
    *   Ensure that when the ORM is initialized with a database configuration but no physical connection, running many concurrent simple queries succeeds without errors.
    *   Ensure that when the ORM is initialized with a database configuration but no physical connection, running many concurrent transactions also succeeds without errors.
*   Implement a locking mechanism to prevent multiple concurrent tasks from initializing the physical connection simultaneously.
    *   Ensure that only one initialization occurs even when many transactions start at the same time.
    *   Allow each transaction to acquire the connection, begin the transaction, execute queries, and commit or rollback cleanly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.