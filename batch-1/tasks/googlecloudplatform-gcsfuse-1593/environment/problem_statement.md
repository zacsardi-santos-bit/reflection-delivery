## Description

The GCS FUSE tool currently merges two separate cache TTL settings — one for stat cache and one for type cache — into a single combined value at startup. This pre-computation happens immediately during flag parsing, which means the original individual values are discarded before they can be passed around or reasoned about independently. It also means any code that needs to determine the effective TTL must rely on this pre-merged value, rather than working from the raw inputs.

Additionally, the configuration-file-based TTL setting has no upper-bound validation. A user can supply an arbitrarily large integer for the TTL in seconds, which may silently result in an invalid or unrepresentable duration value at runtime.

## Expected Behavior

- The stat-cache TTL and type-cache TTL settings should be stored as two independent values rather than being merged into one at flag parsing time.
- Both settings should continue to default to 1 minute when not explicitly configured.
- The logic for computing the effective metadata cache TTL from these two values (plus the configuration-file-based TTL override) should live in a dedicated, reusable function rather than being inlined at each use site.
- When a user provides a TTL value via the configuration file that exceeds the maximum representable duration, the tool should reject the configuration immediately with a clear error message indicating the value is too high and what the maximum supported value is.

## Why This Matters

Keeping the two TTL settings separate allows them to be individually inspected, logged, and used downstream. Centralizing the TTL resolution logic into one function makes the behavior easier to test and reason about. Validating the configuration-file TTL against an upper bound prevents silent failures caused by integer overflow or unrepresentable durations.
