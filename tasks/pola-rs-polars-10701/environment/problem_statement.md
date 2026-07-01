## Description

Polars supports importing data from other dataframe libraries via the standard dataframe interchange protocol, but the current implementation has significant gaps. It relies on a third-party library as an intermediary, which introduces unnecessary dependencies and limitations. Several common cases either fail silently or raise errors that prevent users from importing data they reasonably expect to work.

## Problems with the Current Implementation

- **Categorical columns fail on zero-copy import**: When importing from third-party dataframe libraries, columns containing categorical/dictionary-encoded data raise an error even when the conversion should be possible. These should be mapped to the equivalent enumerated type.
- **Boolean columns from some sources are mishandled**: Boolean data represented in byte-packed format is not handled — either failing when a copy would have been acceptable, or not providing a clear error when copying is not allowed.
- **Temporal types not supported**: Duration and time-of-day column types are not properly handled when importing via the interchange protocol.
- **NaN not treated as null**: Floating-point NaN values in imported data are not converted to Polars null values, which leads to inconsistent behavior.
- **Non-string categorical keys should fail clearly**: Importing categorical data with non-string category keys should produce a clear, actionable error rather than an unexpected crash.
- **Error messages are not specific enough**: When a zero-copy import is not possible, the error message does not explain which column or buffer type is causing the problem.

## Expected Behavior

- Categorical columns should be imported as Enum columns, including when zero-copy is requested (where the data already supports it).
- Empty boolean and empty categorical DataFrames should be importable without copying.
- Boolean columns from byte-packed sources should be importable with copying, and should raise a clear error explaining the limitation when copying is not allowed.
- NaN values in float columns should be treated as null.
- Non-string categorical keys should raise a clear error explaining that they are not supported.
- All zero-copy failures should include specific, descriptive error messages identifying the source of the limitation.

## Why This Matters

Users who call the interchange import function with data from third-party dataframe libraries expect common types to round-trip correctly. The current implementation is unexpectedly restrictive for categorical data and boolean data, and it silently mishandles NaN values in floats.
