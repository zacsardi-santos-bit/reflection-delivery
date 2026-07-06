Implement a feature that automatically wraps each WHERE clause condition in parentheses when a specific configuration option is enabled. This ensures correct boolean logic evaluation in SQL queries involving AND and OR conditions.

*   Update the `BaseDataSourceOptions` interface in `src/data-source/BaseDataSourceOptions.ts`:
    *   Add a new optional read-only boolean property named `isolateWhereStatements`.
    *   This property, when set to true, enables automatic wrapping of each WHERE clause condition in parentheses.

*   Modify the `TestingOptions` interface in `test/utils/test-utils.ts`:
    *   Introduce a new optional boolean property named `isolateWhereStatements`.
    *   This property allows test connections to be configured with WHERE clause isolation.

*   Enhance the `setupTestingConnections` function in `test/utils/test-utils.ts`:
    *   Ensure it propagates `isolateWhereStatements` from `TestingOptions` to the underlying DataSource options.
    *   This should occur when `isolateWhereStatements` is provided and truthy.

*   Implement logic in the query builder:
    *   When a DataSource is created with `isolateWhereStatements: true`, wrap each WHERE clause expression in parentheses.
    *   Specifically, use `.where()` followed by `.andWhere()` to ensure the `andWhere` expression is enclosed in parentheses in the generated SQL.
    *   The first WHERE condition (index 0) should not be wrapped, but subsequent AND and OR conditions must have their expressions wrapped in parentheses when `isolateWhereStatements` is enabled.

*   Ensure the following SQL generation:
    *   A query built with `.where('user.id = :userId').andWhere('user.firstName = :search OR user.lastName = :search')` on a DataSource with `isolateWhereStatements` enabled must produce SQL of the form: `WHERE user.id = ? AND (user.firstName = ? OR user.lastName = ?)`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.