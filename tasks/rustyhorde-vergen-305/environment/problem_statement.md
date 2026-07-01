## Description

The pretty-printing library for build metadata silently discards entries whose keys don't follow the expected naming convention. When a key is not recognized as a standard entry, the internal splitting logic returns nothing rather than preserving the entry. The same problem affects entries with key paths that are shorter than expected — they are dropped entirely.

This means users who add custom build metadata alongside standard version information cannot display it using this library. Custom entries simply disappear from output.

## Expected Behavior

- When the library encounters a key that does not match the standard naming convention, it should preserve that key as a single unsplit entry rather than discarding it.
- When the library encounters a key path that is shorter than expected (too few segments), it should place the entry into a "custom" grouping rather than dropping it.
- Display output for entries in the "custom" group should be non-empty when the entry has a value.
- The macro used to collect build environment variables should support an extended form that allows users to pass additional custom variable names, so those variables are included in the display output alongside the standard entries.

## Why This Matters

Library users who emit custom build metadata alongside the standard output can't currently view that metadata through the pretty-printing interface — it is silently lost. Fixing this makes the library useful for mixed workloads where standard and custom entries coexist.
