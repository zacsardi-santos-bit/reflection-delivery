## Description

When constructing array literals in SQL queries via Polars' SQL interface, typed elements — such as dates, times, timestamps, or numeric values with explicit casts — do not produce list columns with the correct data types in the resulting DataFrame. The element types are not preserved, making it impossible to create properly-typed temporal or numeric list columns directly from SQL array syntax.

## Expected Behavior

- An array literal whose elements are typed temporal values (dates, times, timestamps) should produce a list column whose element type matches those temporal types.
- An array literal whose elements use cast expressions (either the shorthand inline-cast form or the explicit keyword-based cast form) should produce a list column with the correct Polars type for the cast target.
- Nested array literals with typed elements should also preserve their element types through all nesting levels.
- If an array literal contains elements of incompatible types (e.g., a mix of dates and times), an informative error about inconsistent types should be raised rather than silently producing a wrong result.

## Why This Matters

Users writing analytical SQL queries often need to construct lists of temporal or numeric values inline, for example to compare against a set of known dates or to build a reference list. Without proper type propagation in array literals, these queries either fail unexpectedly or produce columns with wrong types that cause downstream errors. Supporting typed literals inside array constructors brings Polars SQL behavior in line with standard SQL expectations and makes it possible to write concise, correct queries involving typed list values.
