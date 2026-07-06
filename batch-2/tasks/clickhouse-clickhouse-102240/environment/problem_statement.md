## Description

ClickHouse crashes with a segmentation fault when querying a view that contains a join, if the session setting for join nullability differs between when the view was created and when it is queried.

## Steps to Reproduce

1. Create two base tables and insert some rows.
2. Wrap one of the tables in a simple view.
3. Create a second view that performs a LEFT JOIN between the first table and the wrapping view, while the session has nullable join columns enabled. The resulting view metadata stores the join output column as nullable.
4. Switch the session to have nullable join columns disabled.
5. Enable the new query analyzer.
6. Query the join view with a filter on the nullable column.

**Result:** ClickHouse crashes with a SIGSEGV / segmentation fault.

**Expected:** The query should return the matching rows without crashing.

## Root Cause

When the query optimizer propagates filters down into views during query planning, it compiles the filter against the column types stored in the view's metadata. If the session setting controlling join nullability has changed since the view was created, the actual execution plan uses different column types than what the compiled filter expects. Applying a filter compiled for nullable columns to non-nullable columns (or vice versa) causes a type-confused crash deep in the execution engine.

## Why This Matters

Users should be able to query views freely regardless of what session settings were active when those views were created. Crashes caused by innocent setting differences are unacceptable in a production database. The fix should ensure that when filter propagation detects a column type mismatch between the filter's expected types and the plan's actual types, the filter is safely skipped rather than applied unsafely.
