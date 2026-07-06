Simplify the Nimby subsystem in the render queue daemon by consolidating multiple implementation classes into a single unified class that uses cross-platform input monitoring. Rename the readiness attribute to accurately reflect its purpose, and update all related code references.

*   Implement a single unified Nimby class in `rqd/rqd/rqnimby.py`:
    *   Replace NimbySelect, NimbyPynput, NimbyNop, and NimbyFactory classes.
    *   Update all references to the old class names to use Nimby.

*   Modify the Nimby class:
    *   `__init__(rqCore)`:
        *   Attempt to import `pynput`. On success, set `is_ready=True`, store `rqCore` as `self.rq_core`, set `locked=False`, initialize `__is_user_active=False`, `__interrupt=False`, `idle_threshold=rqd.rqconstants.MINIMUM_IDLE`, `last_activity_time=time.time()`, and create `pynput.mouse.Listener` and `pynput.keyboard.Listener` instances.
        *   On import failure, set `is_ready=False` and return early.
    *   `run()`:
        *   Return immediately if `is_ready` is False.
        *   If `is_ready` is True, start mouse and keyboard listeners, loop calling `__check_state()` until `__interrupt` is True, and set `is_ready=False` after the loop.
    *   `stop()`:
        *   Set `__interrupt=True` to exit the run loop.
    *   `setup_display()` [static method]:
        *   Set `os.environ['DISPLAY']` to `rqd.rqconstants.DEFAULT_DISPLAY` if not already set.

*   Update attributes and methods:
    *   Rename `active` attribute to `is_ready`.
    *   Rename `rqCore` attribute to `rq_core`.
    *   Remove `isNimbySafeToUnlock()` method from the Machine class and replace references with `isNimbySafeToRunJobs()`.
    *   Ensure `__is_user_active` is initially False and updated by `__on_interaction()` and `__check_state()`.
    *   Ensure `__interrupt` is initially False and set to True by `stop()`.

*   Implement private methods:
    *   `__on_interaction(*args)`:
        *   If `__is_user_active` is False, call `rq_core.onNimbyLock()` and set `locked=True`.
        *   Update `last_activity_time` and set `__is_user_active=True`.
    *   `__check_state()`:
        *   If `__is_user_active` is True and idle time exceeds `idle_threshold`, set `__is_user_active=False`.
        *   If `locked` is True and `__is_user_active` is False, unlock the host if `rq_core.machine.isNimbySafeToRunJobs()` returns True.

*   Add a constant `DEFAULT_DISPLAY` in `rqd/rqd/rqconstants.py` with the value ":0".

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.