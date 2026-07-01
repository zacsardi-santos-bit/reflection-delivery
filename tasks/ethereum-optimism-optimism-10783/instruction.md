Refactor the rollup node driver to implement a modular event-driven architecture. Create a broadcast dispatcher type, function adapters for event handling and emitting, a synchronous event queue, and a step scheduling component with backoff logic.

*   Implement the `Event` interface in `op-node/rollup/events.go` with a `String() string` method.
*   Define the `Deriver` interface with `OnEvent(ev Event)` and the `EventEmitter` interface with `Emit(ev Event)`.
*   Create `DeriverFunc` and `EmitterFunc` function types in `op-node/rollup/events.go`:
    *   `DeriverFunc` should implement `Deriver` by calling itself in `OnEvent(ev Event)`.
    *   `EmitterFunc` should implement `EventEmitter` by calling itself in `Emit(ev Event)`.
*   Develop `SynchronousDerivers` as a slice type `[]Deriver` in `op-node/rollup/events.go`:
    *   Implement `OnEvent(ev Event)` using a pointer receiver to dispatch events to each element.
    *   Ensure an empty `SynchronousDerivers` handles events without errors.
*   Implement `SynchronousEvents` in `op-node/rollup/driver/synchronous.go`:
    *   Implement `rollup.EventEmitter` with `Emit(event rollup.Event)` to queue events.
    *   Define `Drain() error` to process events in FIFO order, appending cyclic events during processing.
    *   Handle context cancellation by discarding new events in `Emit` and returning the context error in `Drain`.
*   Define `sanityEventLimit` as an unexported constant in `op-node/rollup/driver/synchronous.go` to cap the event queue size.
*   Implement `NewSynchronousEvents(log, ctx, root)` to return a `*SynchronousEvents` that dispatches events to `root`.
*   Create `StepReqEvent`, `StepAttemptEvent`, `StepEvent`, and `ResetStepBackoffEvent` structs in `op-node/rollup/driver/steps.go`:
    *   Each struct must implement `rollup.Event` with a `String() string` method.
    *   `StepReqEvent` should include a `ResetBackoff` boolean field.
*   Develop `StepSchedulingDeriver` in `op-node/rollup/driver/steps.go`:
    *   Implement `OnEvent(ev rollup.Event)`, `NextStep() <-chan struct{}`, and `NextDelayedStep() <-chan time.Time`.
    *   Manage step scheduling with exponential backoff and handle reset logic for immediate scheduling.
*   Implement `NewStepSchedulingDeriver(log, emitter)` to return a `*StepSchedulingDeriver` initialized with zero backoff attempts and an empty step channel.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.