## Description

The blank line style checker crashes on Python files that contain "blank" lines with trailing whitespace (e.g., a line holding only a couple of spaces or a single tab character). Instead of reporting style violations normally, the linter panics with an internal assertion failure whenever it encounters one of these whitespace-only lines in a context where blank lines are being accumulated.

This affects all six blank-line rules simultaneously — any file that triggers the faulty code path produces a crash rather than useful diagnostics.

## Expected Behavior

- A line consisting entirely of whitespace characters (no code, no comment) should be treated as a blank line, equivalent to a truly empty line, for the purpose of counting consecutive blank lines.
- When two consecutive "blank" lines are present and one or both happen to contain only whitespace, the appropriate "too many blank lines" violation should be emitted — not a crash.
- When exactly the right number of blank lines (even whitespace-only ones) is present, no spurious violation should be emitted.
- Snapshot output for all six blank-line rules must reflect the corrected behavior and updated line numbers.

## Why This Matters

Real-world Python files often end up with invisible trailing whitespace on otherwise-blank lines (e.g., from editor auto-indent). The linter should handle this gracefully: count such lines as blank, enforce the usual limits, and produce clean, accurate diagnostic output rather than aborting with an internal error.
