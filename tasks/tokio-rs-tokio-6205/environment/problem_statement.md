# Feature Request: Batch Permit Reservation for Bounded MPSC Sender

## Description

The bounded MPSC channel sender currently only allows reserving capacity for a single message at a time. When a producer needs to guarantee space for multiple messages before starting to produce them, it must call the single-slot reservation method repeatedly. This is both inefficient and prevents atomic multi-slot reservation — there is no way to hold a contiguous block of capacity at once.

## Expected Behavior

- A blocking (async) method that reserves `n` channel slots at once, waiting until enough capacity is available, and returning an iterator of individual send permits.
- A non-blocking (synchronous) method that attempts to reserve `n` slots immediately, returning an error if the channel doesn't have enough capacity rather than waiting.
- The returned iterator yields one permit per reserved slot; each permit can be used to send exactly one message.
- Reserving zero slots should succeed immediately as long as the channel is still open, even if the buffer is currently full.
- Attempting to reserve more slots than the channel's total capacity (or more than the system-wide maximum) should fail with an appropriate error.
- When the channel is closed, any reservation attempt should fail, including zero-slot reservations.
- Dropping the iterator before consuming all permits must release the remaining reserved capacity back to the channel and wake any tasks waiting to send.

## Why This Matters

Producers that generate messages in batches need a way to ensure all slots are available before committing to the work of generating the messages. Without batch reservation, there is a risk of producing messages that then cannot be sent, or of holding back-pressure incorrectly. Proper drop semantics (releasing unused permits) ensure there are no capacity leaks when the iterator is discarded early.
