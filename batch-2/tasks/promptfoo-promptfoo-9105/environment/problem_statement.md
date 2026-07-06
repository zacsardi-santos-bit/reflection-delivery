## Description

The prompt suggestion feature currently has no way to request more than one variant at a time. When generating alternative prompts during an evaluation, users should be able to specify how many variants they want — but right now there is no supported count parameter, so you always get a fixed single result.

## Expected Behavior

- Users should be able to specify the number of prompt variants to generate, both via the command line and through the config file.
- The feature should validate the requested count: zero, negative, fractional, and excessively large values should be rejected with a clear error message indicating the valid range (1 to 50).
- When no count is specified, the system should default to generating one variant.
- When requesting multiple variants, the system should handle partial failures gracefully: if some calls succeed and others fail, the successfully generated variants should still be returned rather than discarding everything.
- When all variant generation attempts fail, the system should aggregate the individual error messages into a single combined error.
- A named constant representing the maximum allowed suggestion count (50) should be publicly exported so downstream consumers can reference it.
- Config file settings for both the generate flag and the suggestion count should be properly merged according to a clear precedence order, with the command-line option taking priority over config-file defaults.

## Why This Matters

Without the ability to request multiple variants in a single run, users are forced to invoke the tool multiple times to compare different prompt alternatives. Proper error handling and config-layer merging ensures the feature behaves predictably regardless of how it is invoked.
