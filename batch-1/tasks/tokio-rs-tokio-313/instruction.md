Implement enhancements to the single-threaded executor's turn-based interface to provide polling status and ensure fair scheduling of futures. Modify the `Turn` struct and related methods to expose whether any futures were polled during a turn, and adjust the scheduling to handle all types of ready futures within the same turn.

*   Update the `Turn` struct in `src/executor/current_thread/mod.rs`:
    *   Implement the method `has_polled(&self) -> bool` to return true if any futures were polled during the turn, and false otherwise.

*   Modify the `turn()` method in `CurrentThread` and `Entered` implementations:
    *   Ensure that the `Turn` returned by `turn()` correctly reflects whether any futures were polled by using the `has_polled()` method.
    *   Implement fair scheduling by polling both directly-woken futures and those woken by the park/reactor mechanism within the same `turn()` call.
    *   Use a zero-duration park (non-blocking) when there are pending futures in the scheduler queue to avoid unnecessary blocking.
    *   Ensure that when no futures are ready, `turn()` returns a `Turn` where `has_polled()` is false.

*   Enhance the `CurrentThread` struct:
    *   Implement `new_with_park(park: P) -> CurrentThread<P>` to allow construction with a custom Park implementation.
    *   Ensure the `turn()` method uses the provided park for blocking when no directly-pending futures exist.

*   Ensure the `is_idle(&self) -> bool` method in `CurrentThread` accurately reflects when there are no pending futures.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.