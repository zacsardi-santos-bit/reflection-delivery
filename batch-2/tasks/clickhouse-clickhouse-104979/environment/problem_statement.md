## Description

Querying the system table that lists detached tables while filtering by UUID crashes with an internal logical error when there is at least one detached table in an Atomic database.

## Steps to Reproduce

1. Create a database using the Atomic engine.
2. Create a table and then detach it.
3. Query the system listing of detached tables with a filter on the UUID column (e.g., checking that the UUID does not equal a known zero value).

## Expected Behavior

The query should return the correct count of detached tables matching the predicate, without any errors.

## Actual Behavior

The query throws a severe internal error with a message along the lines of: the UUID argument was expected to have 1 row but has 0. This crash is severe enough to abort the process in debug builds.

## Root Cause

The internal function that builds the result block for detached tables populates the table-name column for each detached entry but does not insert the corresponding UUID value into the UUID column, even when the query planner has allocated a UUID column because the predicate filters on UUID. This causes a column-length mismatch that triggers the logical error.

## Why This Matters

Users who rely on UUID-based filtering to search or inspect their detached tables are completely blocked — any such query crashes rather than returning results. The fix should mirror the UUID insertion logic already present for attached tables.
