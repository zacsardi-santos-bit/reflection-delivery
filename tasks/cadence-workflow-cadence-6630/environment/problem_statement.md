## Description

The timer gate types used by the history queue processor are currently defined as internal types within the queue processing package, with inconsistent APIs and a known resource leak problem. Specifically, the real-time timer gate starts a background goroutine during construction that cannot be stopped, causing goroutine leaks that must be explicitly ignored in tests. Additionally, the event-driven timer gate variant lives alongside unrelated queue logic rather than in the shared clock package where it belongs.

These timer gate abstractions — one backed by real wall-clock time and one driven by external time-advancement events — are generally useful across the codebase and should live in the shared clock package. This would allow other components to reuse them without duplicating logic.

## Expected Behavior

- Both a real-time timer gate and an event-driven timer gate should be available from the shared clock package.
- The real-time variant must expose a notification channel, support updating the target time, querying whether it will fire after a given time, and being stopped cleanly without leaving background goroutines.
- The event-driven variant must expose a notification channel, support updating the target time, advancing the internal "current time" via an explicit method, and querying whether it will fire after a given time.
- The history queue processor must use the new shared implementations, and goroutine leak detection must pass without exceptions.

## Why This Matters

Keeping timer gate logic in the queue package prevents reuse, and the goroutine leak from the constructor is a correctness concern. Moving these to the shared clock package with a clean, consistent interface improves testability, resource safety, and reusability across the system.
