Implement a built-in SQL table function in DuckDB named `test_all_types()` that returns sample data for every supported data type. Ensure it produces three rows: one with minimum values, one with maximum values, and one with all NULLs. Correct existing logic for computing minimum and maximum values for specific data types to ensure accuracy.

Requirements:

* Implement `test_all_types()` in `src/function/table/system/test_all_types.cpp`:
    * Must be callable via `SELECT * FROM test_all_types()` with no arguments.
    * Return exactly 3 rows and 39 columns, one for each supported DuckDB data type.
    * Columns must be in the specified order: BOOLEAN, TINYINT, SMALLINT, INTEGER, BIGINT, HUGEINT, UTINYINT, USMALLINT, UINTEGER, UBIGINT, DATE, TIME, TIMESTAMP, TIMESTAMP_S, TIMESTAMP_MS, TIMESTAMP_NS, DATE_TZ, TIME_TZ, TIMESTAMP_TZ, FLOAT, DOUBLE, DECIMAL(4,1), DECIMAL(9,4), DECIMAL(18,6), DECIMAL(38,10), UUID, INTERVAL, VARCHAR, BLOB, ENUM("small_enum"), ENUM("medium_enum"), ENUM("large_enum"), LIST(INTEGER), LIST(VARCHAR), LIST(LIST(INTEGER)), STRUCT(a INTEGER, b VARCHAR), STRUCT(a LIST(INTEGER), b LIST(VARCHAR)), LIST(STRUCT(a INTEGER, b VARCHAR)), MAP(VARCHAR, VARCHAR).
    * First row: minimum values for each type.
    * Second row: maximum values for each type.
    * Third row: all NULLs.

* Correct minimum and maximum value logic:
    * Ensure DATE, TIMESTAMP, TIMESTAMP_S, TIMESTAMP_MS, DECIMAL, ENUM, BOOLEAN, and TIME types return accurate representable values.
    * Implement static methods `Value::TIMESTAMPNS`, `Value::TIMESTAMPMS`, `Value::TIMESTAMPSEC` in `src/common/types/value.cpp`.
    * Update `Value::MinimumValue()` and `Value::MaximumValue()` for the specified types.

* Implement `TestAllTypesFun` class in `src/function/table/system/test_all_types.cpp`:
    * Use `static void RegisterFunction(BuiltinFunctions &set)` for function registration.

* Implement `GetTestTypes` function in `src/function/table/system/test_all_types.cpp`:
    * Return a vector of `TestType` entries defining each column's name, logical type, minimum value, and maximum value.

* Ensure data persistence:
    * Data from `test_all_types()` must be storable in a DuckDB table and survive a database restart, maintaining byte-for-byte equivalence.

* LogicalType and Value modifications:
    * Add `LogicalType::MAP(LogicalType key, LogicalType value)` overload in `src/common/types.cpp`.
    * Declare in `src/include/duckdb/common/types.hpp`.

* Register `TestAllTypesFun::RegisterFunction` in `src/function/table/system_functions.cpp`.
* Add `test_all_types.cpp` to `src/function/table/system/CMakeLists.txt`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.