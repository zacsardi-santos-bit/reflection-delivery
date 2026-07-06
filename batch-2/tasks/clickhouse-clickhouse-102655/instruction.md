I'm hitting a crash in ClickHouse when I try to use the wildcard-with-exclusion syntax in a SELECT statement where the excluded column name is also used as an alias for a computed expression in the same query.

*   When a SELECT statement uses the wildcard-with-exclusion syntax (e.g., SELECT * EXCEPT (col)) and the same column name also appears as an alias for a computed expression in the same SELECT list, the query must execute successfully without producing any internal server error.

*   The previously failing case caused a LOGICAL_ERROR with message 'Bad cast from type DB::ASTFunction to DB::ASTIdentifier'; this error must no longer occur for such queries.

*   The basic pattern 'SELECT * EXCEPT (c), <expr> AS c FROM t' must return all non-excluded original columns plus the aliased computed expression. For a table with columns (a UInt64, b String, c Float64) and rows (1, 'x', 1.5) and (2, 'y', 2.5), querying SELECT * EXCEPT (c), toFloat64(c) * 2 AS c must return rows: (1, 'x', 3) and (2, 'y', 5).

*   The subquery-in-JOIN pattern must work: a subquery of the form 'SELECT * EXCEPT (c), <expr> AS c FROM t' used as the right side of a LEFT JOIN must return correct results. For the reference table and query, rows (1, 1.5) and (2, 2.5) must be returned.

*   The CTE pattern must work: a Common Table Expression (WITH ... AS (...)) containing 'SELECT * EXCEPT (c), <expr> AS c' that is subsequently joined must return correct results. For the reference table and query, rows (1, 1.5) and (2, 2.5) must be returned.

*   Multiple columns in EXCEPT with corresponding aliases must work: 'SELECT * EXCEPT (b, c), upper(b) AS b, toFloat64(c) * 10 AS c FROM t' must execute and return the correct computed values. For the reference table, rows (1, 'X', 15) and (2, 'Y', 25) must be returned.

*   All of the above query patterns must produce identical correct results regardless of whether the legacy query analyzer (allow_experimental_analyzer = 0) or the new query analyzer (allow_experimental_analyzer = 1) is active.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.