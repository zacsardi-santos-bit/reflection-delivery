## Description

We need a background sensor recording component that can asynchronously consume sensor readings from a shared queue and dispatch them to configurable storage and transmission handlers. Currently, there is no standalone mechanism in the sensors package to handle this kind of producer-consumer pattern in a thread-safe way.

## Expected Behavior

- A new class should be available in the sensors package that runs as a background thread
- The class should accept configurable callbacks for saving and sending sensor readings
- It should expose a queue through which producers (sensor readers) can submit new readings
- For each valid reading, both the save and send callbacks should be invoked with the sensor name and the full reading dict
- Invalid or malformed items placed on the queue must be tolerated and logged as warnings, but must not cause the background thread to crash
- If a save or send callback raises an exception, the error must be logged and the thread must continue processing subsequent readings
- When either or both callbacks are omitted (set to None), valid readings should be silently discarded
- The class should support both daemon and non-daemon thread modes, defaulting to daemon mode
- Lifecycle methods (start, stop, join) must behave as expected for a background thread

## Why This Matters

In a multi-board sensor setup, sensor data is produced by multiple threads and needs to be persisted and relayed asynchronously. Without a dedicated background recording component, sensor data handling becomes ad-hoc and error-prone. This component cleanly decouples data production from recording and transmission, and ensures the system remains resilient to individual handler failures.
