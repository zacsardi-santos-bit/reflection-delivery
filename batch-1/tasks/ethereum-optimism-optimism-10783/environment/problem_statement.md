## Description

The rollup node driver's event handling is currently implemented as a monolithic loop with tightly coupled logic for step scheduling, backoff tracking, and pipeline advancement. This makes individual behaviors hard to test in isolation, and adding new handlers requires modifying the central loop. We need a small, composable event system that allows multiple independent handlers to receive and react to the same events, supports deferred processing of queued events, and manages step scheduling with backoff in a clearly separated component.

## Expected Behavior

- A broadcast type should exist in the core rollup package that holds a list of event handlers and forwards every incoming event to each handler in order. An empty list should be safe to use without panicking.
- A function wrapper type should exist for both event handlers and event emitters so that a plain function can be used anywhere an interface is expected, without defining a named type.
- A synchronous event queue should buffer events emitted during processing and drain them in order on demand. If the context is cancelled, draining should stop immediately and return the context error. Events emitted by handlers during a drain (i.e. recursive/cyclic events) should also be processed in that same drain call. A configurable safety limit should prevent the queue from growing without bound — events beyond the limit are dropped.
- A step scheduling component should decouple "someone requested a step" from "a step should actually run now." It should expose two channels: one for immediate steps and one for delayed (backoff) steps. After each step attempt it should increase a backoff counter so subsequent requests are routed to the delayed channel instead of the immediate one. Sending a reset signal (either inline with a request or as a standalone event) should clear the backoff and allow the next request to be scheduled immediately.

## Why This Matters

Without this foundation, every new handler must be wired into the monolithic event loop, and the backoff logic cannot be unit-tested without spinning up the whole driver. These primitives allow each piece of logic to be verified independently and composed into the driver without changing the loop itself.
