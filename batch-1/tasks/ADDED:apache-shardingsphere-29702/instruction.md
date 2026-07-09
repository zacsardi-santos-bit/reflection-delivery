Implement support for additional SQLServer dialect SQL patterns in the ShardingSphere SQL parser. Enhance the parser to recognize and correctly process specific INSERT and SELECT statement patterns that are currently unsupported.

*   Update the SQLServer dialect parser to support parsing INSERT statements with a WITH clause containing table hint keywords.
    *   Ensure the parser recognizes syntax like `INSERT INTO table WITH (TABLOCK) SELECT ...`.
    *   Implement a `WithTableHintSegment` class in `parser/sql/statement/src/main/java/org/apache/shardingsphere/sql/parser/sql/dialect/segment/sqlserver/hint/WithTableHintSegment.java`.
        *   Constructor: `WithTableHintSegment(int startIndex, int stopIndex)`
        *   Methods: `getStartIndex()`, `getStopIndex()`, `getTableHintLimitedSegments()`
    *   Implement a `TableHintLimitedSegment` class in `parser/sql/statement/src/main/java/org/apache/shardingsphere/sql/parser/sql/dialect/segment/sqlserver/hint/TableHintLimitedSegment.java`.
        *   Constructor: `TableHintLimitedSegment(int startIndex, int stopIndex)`
        *   Methods: `getStartIndex()`, `getStopIndex()`, `getValue()`, `setValue(String value)`
    *   Extend `SQLServerInsertStatement` in `parser/sql/statement/src/main/java/org/apache/shardingsphere/sql/parser/sql/dialect/statement/sqlserver/dml/SQLServerInsertStatement.java` to store and expose a `WithTableHintSegment`.
        *   Methods: `getWithTableHintSegment()`, `setWithTableHintSegment(WithTableHintSegment withTableHintSegment)`
    *   Extend `InsertStatementHandler` in `parser/sql/statement/src/main/java/org/apache/shardingsphere/sql/parser/sql/dialect/handler/dml/InsertStatementHandler.java` with a method to retrieve the `WithTableHintSegment`.
        *   Method: `getWithTableHintSegment(InsertStatement insertStatement) -> Optional<WithTableHintSegment>`
        *   Return `Optional.empty()` for non-SQLServer insert statements or those without a WITH clause.

*   Enhance the SQLServer dialect parser to support INSERT statements using EXEC to invoke a stored procedure with named parameters.
    *   Ensure parsing of syntax like `INSERT INTO table EXEC proc_name @param = value, ...`.

*   Update the SQLServer dialect parser to support SELECT statements with complex arithmetic expressions.
    *   Ensure parsing of expressions involving type-cast functions applied to built-in system function calls.

*   Extend the SQLServer dialect parser to handle SELECT statements with:
    *   JOIN operations.
    *   WHERE clauses with compound conditions using LIKE.
    *   GROUP BY clauses.
    *   HAVING clauses using aggregation comparisons.
    *   ORDER BY clauses.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.