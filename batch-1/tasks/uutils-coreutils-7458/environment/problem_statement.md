## Description

The `printf` utility does not correctly format non-finite floating-point values (positive infinity, negative infinity, and NaN) when format specifiers include field width or padding options.

Two specific bugs exist:

1. **Space-sign flag is not applied to non-finite values**: When a format string includes a space as a sign indicator (requesting that positive values be prefixed with a space instead of a plus sign), this space is not applied to positive infinity or positive NaN. The result ends up one character short of the specified width.

2. **Zero-padding flag should be ignored for non-finite values**: When a format string requests zero-padding (filling unused width with zero characters), the utility currently applies that zero-padding to non-finite values. However, according to POSIX behavior, zero-padding must be suppressed for non-finite values and replaced with space-padding. The current behavior produces incorrect output like '00inf' instead of '  inf'.

## Expected Behavior

- Formatting a positive non-finite value (such as infinity or NaN) with a space-sign flag and a field width of 5 should produce output right-aligned in a field of 5 characters with a leading space as the sign indicator.
- Formatting a non-finite value with zero-padding and a field width of 5 should produce the same output as space-padding — spaces, not zeros, fill the unused width.
- Negative non-finite values should continue to receive a minus sign and be right-padded with spaces.

## Why This Matters

These bugs cause incorrect output whenever scripts or programs use `printf` to format special floating-point values with width specifiers, which is common in numerical output pipelines. The behavior should match the POSIX standard and standard C library printf behavior.
