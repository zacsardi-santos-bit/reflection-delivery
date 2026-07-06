## Description

The current rate limiting API for slot acquisition returns an opaque result object even when no slot was actually obtained — callers must then inspect a "releasable" flag on the object to determine whether they really hold a slot. This is confusing and error-prone. There is also no clean way to automatically release a slot when a code block finishes, requiring explicit release calls that are easy to forget.

## Expected Behavior

- A failed slot acquisition should return nothing, so callers can use a simple absence check instead of interrogating a flag on the returned object.
- A successful slot acquisition should return a closeable resource that releases the slot automatically when the block exits.
- When all slots are occupied, an attempt to acquire one should block for the configured timeout period and only then return nothing — it must not return immediately.
- When a slot is available, acquisition should complete quickly (well within the configured timeout).
- The number of concurrently held slots must never exceed the configured total allowed, and the number of borrowed slots must never exceed the difference between total allowed and guaranteed, even under heavy concurrent load.
- After all slots are released, the rate limiter should report that it is empty.
- Guaranteed slots must remain available for their native request type even when the borrow quota is fully consumed by another request type.

## Why This Matters

The current API forces every caller to write boilerplate flag-checking code and manage slot lifecycle manually. Switching to an absent-result-means-failure, closeable-means-success model brings the slot-acquisition pattern in line with standard resource-management idioms, reducing mistakes and making the intent immediately clear. The concurrent-count invariants and timeout behavior also need to be reliably enforced so that rate limiting actually protects the system under load.
