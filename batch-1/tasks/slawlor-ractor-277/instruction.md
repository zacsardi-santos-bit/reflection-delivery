Implement methods in the `ActorCell` type to manage the shutdown of supervisor actors' children and perform runtime message type checks. Ensure these methods are accessible from typed actor references as well.

*   Implement the `is_message_type_of<TMessage>() -> Option<bool>` method in `ActorCell`:
    *   Return `Some(true)` if the local actor's message type matches `TMessage`.
    *   Return `Some(false)` if the local actor's message type does not match `TMessage`.
    *   Return `None` for remote actors where a runtime check is not possible.

*   Implement child management methods in `ActorCell`:
    *   `stop_children(reason: Option<String>)`:
        *   Send stop signals to all linked children without waiting for their exit.
        *   Trigger the parent actor to shut down after children are stopped and the supervision tree is notified.
    *   `stop_children_and_wait(reason: Option<String>, timeout: Option<Duration>)`:
        *   Send stop signals to all linked children and wait for their full exit, including running each child's `post_stop` lifecycle hook.
        *   Ensure this method is safe to call from within a supervisor's `post_stop` hook.
    *   `drain_children()`:
        *   Send drain signals to all linked children without waiting for their exit.
        *   Trigger the parent actor to shut down after children drain and the supervision tree is notified.
    *   `drain_children_and_wait(timeout: Option<Duration>)`:
        *   Send drain signals to all linked children and wait for their full exit, including running each child's `post_stop` lifecycle hook.
        *   Ensure this method is safe to call from within a supervisor's `post_stop` hook.

*   Ensure that when `stop_children_and_wait` or `drain_children_and_wait` is called during a parent's `post_stop` hook, each child's `post_stop` lifecycle hook is invoked exactly once before the parent finishes shutting down.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.