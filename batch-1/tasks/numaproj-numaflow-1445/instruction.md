Implement updates to the streaming pipeline framework to enhance windowing strategies and introduce jitter in message generation. Update constructors for windowing strategies to include vertex context, and modify the tick generator to support jitter for simulating out-of-order messages.

*   Update Windower Constructors:
    *   Modify the fixed windower constructor in `pkg/window/strategy/fixed/fixed.go`:
        *   Accept a `*dfv1.VertexInstance` as a second parameter alongside the window length duration.
        *   Ensure existing windowing behaviors (assigning, inserting, closing, deleting) remain unchanged.
    *   Modify the session windower constructor in `pkg/window/strategy/session/session.go`:
        *   Accept a `*dfv1.VertexInstance` as a second parameter alongside the gap duration.
        *   Maintain existing session windowing behavior.
    *   Modify the sliding windower constructor in `pkg/window/strategy/sliding/sliding.go`:
        *   Accept a `*dfv1.VertexInstance` as a third parameter, after length and slide durations.
        *   Preserve existing sliding windowing behavior.

*   Update Tick Generator for Jitter Support:
    *   Modify the `timeFromNanos` function in `pkg/sources/generator/tickgen.go`:
        *   Accept a second parameter of type `time.Duration` representing jitter.
        *   When jitter is zero and the nanosecond timestamp is positive, return a `time.Time` with the exact nanosecond value.
        *   When the nanosecond timestamp is non-positive, return the current time.
    *   Enhance the tick generator configuration to support an optional jitter duration:
        *   When jitter is not set, ensure generator behavior remains unchanged.
        *   When jitter is set to a positive duration, allow generated message event times to be randomly delayed by up to that duration.

*   Ensure all callers of windower constructors within the reduce pipeline packages (`pkg/reduce` and `pkg/reduce/pnf`) are updated to pass a vertex instance when constructing any windower type. This ensures existing reduce data-forwarding tests continue to compile and pass.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.