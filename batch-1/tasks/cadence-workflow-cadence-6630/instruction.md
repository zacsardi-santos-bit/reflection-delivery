Refactor the timer gate components used by the history queue processor by moving them into the shared clock package. Implement both a real-time and an event-driven timer gate with consistent interfaces, ensuring no background goroutines are leaked. Update the history queue processor to utilize these new implementations.

*   Define a `TimerGate` interface in `common/clock/timer_gate.go`:
    *   Methods: `Update(t time.Time) bool`, `Chan() <-chan time.Time`, `FireAfter(t time.Time) bool`, `Stop()`.
*   Implement `NewTimerGate(timeSource TimeSource) TimerGate`:
    *   Construct a real-time timer gate without starting any background goroutines.
*   Implement `TimerGate` methods:
    *   `Update(t time.Time)` returns `true` if the update is accepted, `false` otherwise.
    *   `Chan()` returns a channel that signals when the timer fires.
    *   `FireAfter(t time.Time)` returns `true` if the timer will fire after the given time.
    *   `Stop()` cancels the active timer, ensuring no signals are received until `Update()` is called again.
*   Define an `EventTimerGate` interface in `common/clock/event_timer_gate.go`:
    *   Methods: `Update(t time.Time) bool`, `Chan() <-chan time.Time`, `SetCurrentTime(t time.Time) bool`, `FireAfter(t time.Time) bool`.
*   Implement `NewEventTimerGate(currentTime time.Time) EventTimerGate`:
    *   Construct an event-driven timer gate initialized with the given current time.
*   Implement `EventTimerGate` methods:
    *   `Update(t time.Time)` returns `true` if the new timer is earlier than or equal to the current time.
    *   `Chan()` returns a channel that signals when the timer fires.
    *   `SetCurrentTime(t time.Time)` returns `true` if the time advances past the current internal time.
    *   `FireAfter(t time.Time)` returns `true` only when an active timer is set to fire after the given time.
*   Update `timerQueueProcessorBase` in `service/history/queue`:
    *   Use `clock.TimerGate` for the `timerGate` field.
    *   Replace `FireChan()` with `Chan()` for reading the timer channel.
    *   Replace `Close()` with `Stop()` for stopping the gate.
    *   Use `clock.NewTimerGate(timeSource)` for creating the timer gate.
*   Ensure goroutine leak tests pass without exceptions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.