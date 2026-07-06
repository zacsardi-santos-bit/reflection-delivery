## Description

Computing the average of a sparse array currently ignores the option to control how missing values are handled. When a user passes a flag to indicate that missing values should not be skipped, the computation proceeds as if the flag were not there — silently returning the mean of non-missing values instead of propagating the missing value as the result.

This behavior is inconsistent with how other numeric reductions in the library work: operations like sum, min, and max already respect the missing-value skip option and return a missing result when the option is disabled and missing values are present.

## Expected Behavior

- When the missing-value skip option is enabled (the default), the mean should be computed over non-missing values only.
- When the missing-value skip option is disabled and the array contains any missing values, the mean should return a missing result rather than silently ignoring the missing values.
- When the array's element type does not support arithmetic (e.g., a mix of strings and missing values), a clear type error should be raised, regardless of the missing-value skip setting.

## Why This Matters

Users who rely on consistent reduction behavior across different array backends may produce incorrect results if sparse arrays silently ignore the option to not skip missing values. Fixing this ensures sparse arrays behave the same as their dense counterparts for the mean operation.
