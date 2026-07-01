## Description

When writing PRQL queries targeting Microsoft SQL Server that combine deduplication (via grouping to keep only one row per group) with row-count pagination, the PRQL compiler generates SQL that is rejected by MSSQL at runtime.

MSSQL has a stricter requirement than most SQL dialects: when a query uses DISTINCT, any expression referenced in the ORDER BY clause must also appear in the SELECT list. The compiler currently inserts a special placeholder expression into the ORDER BY clause when no explicit sort order is given — a workaround that is valid for other databases but that MSSQL forbids when DISTINCT is in use.

## Expected Behavior

- When a PRQL query targets the MSSQL dialect and the compiled output uses DISTINCT together with FETCH-style pagination, the ORDER BY clause should reference an actual column from the SELECT list rather than the placeholder.
- The first projected column should be used as the ORDER BY expression in this case.
- The generated SQL must be valid and executable on MSSQL without modification.
- Row-count limits should be translated to MSSQL's OFFSET/FETCH pagination syntax (OFFSET 0 ROWS, FETCH FIRST N ROWS ONLY).
- Column names must be double-quoted in both the SELECT list and the ORDER BY clause.

## Why This Matters

Users targeting MSSQL who combine deduplication patterns with pagination in their PRQL queries currently receive SQL that MSSQL will reject at runtime. This is a correctness bug for an important SQL dialect, and producing valid dialect-specific SQL is a core part of PRQL's value.
