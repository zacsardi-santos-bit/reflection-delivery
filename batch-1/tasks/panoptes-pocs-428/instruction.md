Implement a background sensor recording component in the `pocs.sensors.sensor_recorder` module. This component should run as a background thread, asynchronously consuming sensor readings from a shared queue and dispatching them to configurable storage and transmission handlers. Ensure the component is resilient to errors and can handle invalid data gracefully.

Requirements:

*   Create a `SensorRecorder` class in `pocs/sensors/sensor_recorder.py`.
    *   Extend `threading.Thread`.
    *   Constructor signature: `__init__(self, save_func, send_func, logger, daemon=True, queue_read_timeout=2.0, **kwargs)`.
        *   `save_func`: callable or None.
        *   `send_func`: callable or None.
        *   `logger`: object with `info`, `warning`, `error` methods.
        *   `daemon`: bool, default True.
        *   `queue_read_timeout`: float, default 2.0.
    *   After construction, `is_alive()` must return False until `start()` is called.
    *   After `start()` is called, `is_alive()` must return True.

*   Implement a `queue()` method.
    *   Returns a `Queue` object for submitting readings.
    *   Valid reading: dict with 'name', 'timestamp', 'data' keys (all truthy).

*   Implement a `stop_recorder()` method.
    *   Signals the thread to stop.
    *   After `stop_recorder()` and `join()`, `is_alive()` must return False.

*   Ensure the thread operates correctly:
    *   Default to daemon mode unless specified otherwise.
    *   Log exactly 2 info-level messages per run (on start and stop).
    *   Handle invalid queue items:
        *   Non-dict items: 1 warning-level log call each.
        *   Dict items with invalid/missing fields: 1 warning-level log call each.
    *   Handle exceptions in callbacks:
        *   If `save_func` raises an exception, log 3 error-level messages.
        *   If `send_func` raises an exception, log 3 error-level messages.
    *   If both callbacks are None, discard valid readings silently.

*   Use the logger object correctly:
    *   Pass a format string and at least one additional argument for each log call.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.