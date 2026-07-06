## Description

Running the DuckDB test suite currently requires knowing two separate things: where the Python helper script lives and where the compiled test binary lives. Both must be passed explicitly on the command line. This is error-prone and forces every CI workflow and script that runs tests to hardcode two distinct paths rather than just one.

Additionally, when a test fails the current output is hard to read: result mismatches are crammed into a single-line summary mixing the expected value, actual value, and diagnostic message all together. Crash failures due to signals or sanitizer errors lack any structured description. Code snippets shown on failure often include unnecessary shared leading whitespace that makes them harder to scan. Skipped test counts can appear mixed in with failure detail blocks rather than being cleanly separated into the final summary.

## Expected Behavior

- After building, tests can be run using a single short path in the build output directory. The wrapper automatically locates the compiled test binary next to itself — no explicit binary path argument needed.
- CI workflows that invoke the test runner directly are updated to use this new wrapper path.
- When a test fails due to a result mismatch, the output displays the expected and actual values in clearly labeled separate sections rather than as a compressed single-line summary.
- When a test process crashes with a signal, the failure output includes a meaningful description of the crash type rather than giving no indication of what happened.
- When sanitizer output is present alongside a crash, the sanitizer details are shown instead of a generic signal description.
- Code snippets included in failure output have shared indentation stripped so they display cleanly without excess leading whitespace.
- Skipped test counts appear only in the final run summary, not mixed into individual failure blocks.

## Why This Matters

Developers running tests locally benefit from a simpler, discoverable entrypoint. CI configuration becomes easier to maintain. The improved failure output makes it faster to diagnose what went wrong without having to re-run the test or inspect raw log files.
