## Description

Plan Mode is currently treated as an experimental feature that requires users to explicitly opt in via settings before they can use it. This creates unnecessary friction — most users don't discover the feature, and those who try to use it via the command-line flag find it silently ignored unless they've first enabled the experimental toggle.

## Expected Behavior

- Plan Mode should be enabled by default — users should be able to use it without changing any settings.
- When the plan approval mode is selected via the command-line, it should take effect immediately without any experimental gating.
- The settings entry for plan should reflect this new default (enabled by default) and its description should be updated to reflect that it is a stable, opt-out feature rather than an experimental opt-in.
- The list of selectable approval modes should include plan mode alongside the other available modes.

## Why This Matters

Plan Mode is a useful feature for safely exploring and designing solutions before making changes. Hiding it behind an experimental flag reduces its discoverability and prevents users from benefiting from it by default. Making it available out of the box, with no configuration required, improves the overall user experience and aligns the feature's availability with its maturity.
