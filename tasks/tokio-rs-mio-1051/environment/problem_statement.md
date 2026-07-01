## Description

Currently, sharing the I/O registry across multiple threads requires cloning it in a way that relies on the registry's internal reference-counting. This approach is confusing because it looks like a simple value copy, yet both the original and the clone share the same underlying resources. More importantly, the types involved do not formally advertise themselves as thread-safe, so placing them inside standard shared-ownership containers is either rejected at compile time or requires unsafe workarounds.

## Expected Behavior

- The registry and poll types should satisfy the language's standard thread-safety requirements, making it straightforward to share them across threads using standard shared-pointer containers.
- When a truly independent registry handle is needed (for example, to hand off to a background thread that registers I/O sources), there should be an explicit, fallible mechanism for creating one — clearly communicating that the duplication can fail rather than silently succeeding.
- Concurrent registration from multiple threads and polling on the main thread should work correctly and produce the expected readiness events.

## Why This Matters

Without these guarantees, developers who want to register I/O sources from a thread pool or a secondary thread while polling happens elsewhere are forced into non-obvious patterns. Making the types formally thread-safe and providing a clear cloning API removes those workarounds and makes the intended multi-threaded use case idiomatic and safe.
