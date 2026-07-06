Implement the ability to pass bound parameter values when creating a pending query in DuckDB's asynchronous query execution system. Enhance error reporting for catalog-level errors and type conversion issues, and improve error messages for prepared statements with missing parameter values.

*   Update the `Connection` class to include a `PendingQuery` method with the following signature:
    *   `PendingQuery(const string &query, vector<Value> &values, bool allow_stream_result = false) -> unique_ptr<PendingQueryResult>`
    *   `PendingQuery(unique_ptr<SQLStatement> statement, vector<Value> &values, bool allow_stream_result = false) -> unique_ptr<PendingQueryResult>`
    *   Implement these in `src/include/duckdb/main/connection.hpp` (declaration) and `src/main/connection.cpp` (implementation).

*   Ensure the `PendingQuery` method:
    *   Accepts a parameterized query string with '?' placeholders and a vector of `duckdb::Value` objects as bound parameter values.
    *   Returns a `PendingQueryResult` that can be checked for errors using `HasError()` and `GetErrorType()`.

*   Handle specific error scenarios:
    *   For catalog errors (e.g., non-existent table), ensure `PendingQueryResult` has `HasError()` return true and `GetErrorType()` return `ExceptionType::CATALOG`.
    *   For type conversion errors (e.g., incompatible value types), ensure `PendingQueryResult` initially has `HasError()` return false, but executing it with `Execute()` should result in `HasError()` being true and `GetErrorType()` returning `ExceptionType::CONVERSION`.
    *   For an empty query string, ensure `PendingQueryResult` indicates failure (either `HasError()` is true or the call fails).

*   Support transactions:
    *   Allow an empty values vector for non-parameterized statements like `BEGIN TRANSACTION` and `COMMIT`.
    *   Ensure transaction isolation is respected: changes in one connection's open transaction should not be visible to another connection with its own open transaction.

*   Improve error messages for prepared statements:
    *   When executed with fewer bound values than required, raise an `InvalidInputException` with a message: "Values were not provided for the following prepared statement parameters: N", where N is the index of the missing parameter(s).

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.