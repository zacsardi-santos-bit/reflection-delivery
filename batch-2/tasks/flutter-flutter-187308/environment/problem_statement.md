## Description

Flutter tooling currently fails to handle two important error scenarios gracefully, leaving developers and CI pipelines without clear feedback on what went wrong.

**Issue 1: Analysis server crash not properly signaled**

When the static analysis server process exits unexpectedly (for example, due to a fatal internal error), the analyze command does not propagate the failure as a proper tool-level exit. Instead, the error may be swallowed or surfaced as a generic exception without an associated exit code. This means scripts or CI checks that rely on the exit code to detect analysis failures may not behave correctly.

**Issue 2: Web debug service timeout not handled**

When running a Flutter web app in debug mode, if the web debug service connection times out before it can be established, the runner currently crashes with an unhandled exception. There is no user-friendly error message indicating a connection timeout occurred.

## Expected Behavior

- When the analysis server exits prematurely with a specific error code, the tool should exit cleanly with that same exit code and include a message indicating the server exited with that code along with any captured output.
- When a debug connection to the web debug service times out, the runner should exit cleanly with a clear message stating it failed to connect, and should also log a more detailed description of the timeout error.

## Why This Matters

These two scenarios are real failure modes that developers encounter. Without proper error handling, troubleshooting is much harder — there's no clear indication of why the tool exited or what to fix. Proper error propagation ensures developers get actionable feedback and CI pipelines can detect failures reliably.
