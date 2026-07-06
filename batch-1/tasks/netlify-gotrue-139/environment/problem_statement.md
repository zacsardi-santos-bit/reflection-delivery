## Description

The storage layer needs a utility that, given a database-mapped model struct and a list of field names to include in an update, can determine which columns should be excluded from the operation. Currently, there is no clean way to perform a selective column update that explicitly skips certain fields — developers have to manage this logic manually or update all columns every time.

## Expected Behavior

- A utility function should accept a struct value and one or more database column names (as identified by their database struct tag annotations) to include in an update.
- The function should return all other column names from the struct — i.e., those not in the include list — so they can be passed as excluded columns to the underlying database operation.
- If a column name that doesn't exist on the struct is provided, the function should return an error to prevent silent failures.

## Why This Matters

This capability is essential for supporting partial updates on database records, where only specific fields should be persisted while others remain unchanged. Without it, it is difficult to build correct, efficient upsert or update logic that intentionally skips certain columns. Proper error reporting when an invalid column name is specified also prevents subtle data integrity bugs.
