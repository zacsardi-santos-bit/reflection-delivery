## Description

After upgrading to a newer major version of the dataframe library, several tests in the dataframe and table management components are failing. The library classifies columns by their data type and reports this metadata to consumers, but some of this classification logic assumed specific string representations for certain data types that have changed in the newer version.

In particular, timedelta-typed columns were matched using an exact dtype string that only matches one specific time resolution. Since the newer library version reports the same logical type but with a different resolution string, the classification fails to recognize these columns correctly. Similarly, string columns now report a different dtype name than before, and null values in timedelta/datetime columns now serialize to JSON as proper null values rather than as a special text representation.

## Expected Behavior

- The type classification system should correctly identify timedelta columns regardless of what time resolution appears in their dtype string, returning the appropriate semantic type for them.
- Tests that compare field type metadata should accept valid output from either library version without failing.
- JSON serialization snapshots should handle the way newer library versions represent null timestamp/duration values.
- The full test suite should pass when running against a recent major version of the dataframe library.

## Why This Matters

Users who upgrade the dataframe library to a recent major version will encounter broken behavior in the table and dataframe display components. The root cause is fragile dtype string matching that doesn't account for how the same logical type can be expressed with different precision qualifiers across library versions.
