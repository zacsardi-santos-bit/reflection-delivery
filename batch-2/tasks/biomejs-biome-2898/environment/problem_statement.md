## Description

The tooling currently uses "apply" and "apply-unsafe" flags to trigger automatic code corrections. While these work, many developers coming from other ecosystems expect a simpler "fix" convention, and having separate flags for "safe" and "unsafe" fixes tied together in a combined flag name makes it harder to express intent clearly.

We should introduce a "fix" flag as a more intuitive alias for the write/apply behavior across all major subcommands (check, format, lint, and migrate). Additionally, a standalone "unsafe" flag would let users separately opt into riskier automatic corrections without having to use a combined flag name.

## Expected Behavior

- A "fix" flag should be available for the check, format, lint, and migrate subcommands and behave exactly like the existing write flag for each.
- An "unsafe" flag should be available on check and lint to allow unsafe fixes in combination with "fix" or the existing write flag.
- The old flags ("apply", "apply-unsafe") should continue to work as backward-compatible aliases so existing scripts are not broken.
- When a user mistakenly passes both the new flag and an equivalent old flag at the same time (e.g., "fix" together with "apply", or "fix" together with "write"), the tool should clearly reject the invocation with an informative incompatibility error rather than silently accepting it.
- When auto-fixing is requested but some issues cannot be automatically corrected, the tool should still write all fixes it can apply and then report a non-zero exit code listing the unfixed errors.

## Why This Matters

The "fix" convention is widely recognized from other linters and formatters. Adding it as a supported flag improves discoverability and makes the tool feel more familiar to new users, without breaking anyone who already relies on the existing flag names.
