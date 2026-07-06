## Description

The Python bindings for the event-based messaging pattern are incomplete. While it is possible to build and open an event service, there are no Python port types for sending or receiving notifications. Developers using the Python API cannot create a notifier to trigger events or create a listener to receive them, making the event messaging pattern entirely unusable from Python.

## Expected Behavior

- The event service object should expose a notifier factory and a listener factory so that port instances can be created.
- The notifier factory should allow setting a default event identifier before creating the notifier port.
- Once created, the notifier should be able to send a notification using either its preconfigured default identifier or an explicitly supplied one.
- The listener should support receiving events in several modes: a non-blocking attempt to get one event, a non-blocking attempt to get all pending events, a time-limited wait for one event, a time-limited wait for all pending events, a fully blocking wait for one event, and a fully blocking wait for all pending events.
- Events received by the listener should be returned in the order they were sent.
- Both the notifier and the listener should expose the deadline duration that was configured on the service at creation time.

## Why This Matters

Without these port types, Python developers cannot use the event messaging pattern at all. Adding the notifier and listener ports brings the Python API to feature parity with the rest of the library for event-based communication, enabling real-time notification workflows to be implemented in Python.
