Implement a system to dynamically resolve placeholders in JDBC connection strings using shared notebook variables in the BeakerX SQL kernel. Ensure that sensitive information like credentials can be managed securely without hardcoding them in notebooks.

*   Define the `DataSourceParamResolver` interface:
    *   Declare a method `resolve(String jdbcTemplate)` that returns a resolved JDBC connection string.

*   Implement the `DataSourceParamResolverImpl` class:
    *   Implement `DataSourceParamResolver`.
    *   Accept a `BeakerXClient` as the sole constructor argument.
    *   Implement the `resolve(String jdbcTemplate)` method to:
        *   Replace each placeholder `{$beakerx.VARIABLE_NAME}` in the template with the value from `BeakerXClient.get(VARIABLE_NAME)`.
        *   Example: Convert 'jdbc:postgresql://localhost:5432/postgres?user={$beakerx.dbuser}&password={$beakerx.dbpassword}' to 'jdbc:postgresql://localhost:5432/postgres?user=user1&password=user1password' given `dbuser='user1'` and `dbpassword='user1password'`.

*   Update `DataSourcesMagicCommand` class:
    *   Modify the constructor to accept `DataSourceParamResolver` as the second argument after `KernelFunctionality`.
    *   Ensure that when executing a datasource command, `paramResolver.resolve()` is called on the JDBC URL before storing it.

*   Update `DefaultDataSourcesMagicCommand` class:
    *   Modify the constructor to accept `DataSourceParamResolver` as the second argument after `KernelFunctionality`.
    *   Ensure that `resolve()` is called exactly once on `DataSourceParamResolver` when `execute()` is invoked.

*   Implement `BeakexClientTestImpl` class in `EvaluatorTest`:
    *   Implement `set(String name, Object value)` to store and return the value by name.
    *   Implement `get(String name)` to retrieve and return the previously stored value for that name.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.