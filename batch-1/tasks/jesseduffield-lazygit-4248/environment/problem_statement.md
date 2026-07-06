## Description

The project relies on an external library that provides a fast way to look up the current goroutine's ID. However, this library has not been updated to reflect the internal goroutine structure introduced in recent versions of Go. As a result, when running on a newer Go release, the fast goroutine ID lookup silently returns an incorrect value — one that does not match the ID the Go runtime itself reports.

This mismatch means any feature that depends on accurate goroutine identification (such as deadlock detection, which is also used in this project) may behave incorrectly or produce misleading output.

## Expected Behavior

- The fast goroutine ID lookup should return the exact same goroutine ID that the Go runtime reports when you inspect the current goroutine's stack trace.
- A related deadlock-detection library that depends on the goroutine ID functionality should also be updated to a version compatible with the updated goroutine ID library.

## Why This Matters

Incorrect goroutine IDs undermine any tooling built on top of them. Deadlock detection, goroutine tracking, and debugging all become unreliable if the underlying ID lookup is returning wrong values. Updating the libraries to versions that correctly support the current Go runtime restores correctness.
