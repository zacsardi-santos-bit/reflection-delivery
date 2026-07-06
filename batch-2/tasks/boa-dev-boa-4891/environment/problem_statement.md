## Description

The JavaScript engine's event loop currently only supports a synchronous, blocking execution model. When you call the method to run all pending jobs, it blocks the calling thread until every job in the queue is completely finished. This is fine for simple scripts, but it breaks down the moment you want to embed the engine inside a larger async Rust application that needs to interleave JavaScript execution with other async work — things like reading user input from a channel, handling network requests, or coordinating with other async tasks running in the same executor.

The problem is that the current "run all jobs" operation cannot yield control back to the outer async runtime between processing cycles. An async JavaScript job that repeatedly yields (e.g., while waiting for some condition) holds the event loop captive and prevents other async Rust work from making progress.

## Expected Behavior

- The event loop should be drivable from an async context, yielding control between job processing iterations so that other async tasks can run in between.
- An async job that continuously yields to the executor should not block other enqueued jobs from being processed — those jobs should be picked up in the next cycle.
- When all pending jobs have been processed and nothing remains in the queue, the event loop should signal completion rather than continuing to spin.
- It should be possible to poll the event loop one iteration at a time from an async context, checking whether it has completed or is still running.

## Why This Matters

Embedders and custom runtime authors need to build event loops that process JavaScript jobs alongside async I/O without one blocking the other. The test infrastructure also needs the ability to write tests that drive async JavaScript scenarios with fine-grained async control from Rust.
