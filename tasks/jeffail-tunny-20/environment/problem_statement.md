## Description

The current goroutine pool library has an overly verbose and error-prone API. Creating a pool requires chaining multiple calls, checking errors that are rarely meaningful in practice, and manually managing lifecycle state. The old interface also requires users to implement multiple optional, fragmented interfaces just to get basic lifecycle hooks like cleanup or timeout handling.

## Expected Behavior

- Creating a pool should be a single, simple constructor call that returns a ready-to-use pool — no separate "open" step, no error to check on creation.
- Submitting work synchronously should return only the result, not an error. If the pool is closed, the caller should receive a clear panic rather than a silent error.
- A timed variant of job submission should exist that returns the result and an error; it should return a recognizable error when the job times out and another when the pool is not running.
- The pool size should be adjustable at any time — both increasing and decreasing the number of active workers — without restarting the pool.
- A way to query the current pool size should be available.
- Custom workers should implement a single, unified interface covering: synchronous job processing, readiness blocking before each job, interruption when a job is cancelled, and cleanup when the worker is removed from the pool.
- A convenience constructor should exist for the common case of processing closures as jobs, where non-callable payloads produce a recognizable sentinel value.

## Why This Matters

The old API forced users to handle meaningless errors and manage lifecycle complexity that the library could handle internally. These changes make the library much easier to use correctly, and make it possible to cleanly resize a live pool or implement stateful per-goroutine workers with proper cleanup.
