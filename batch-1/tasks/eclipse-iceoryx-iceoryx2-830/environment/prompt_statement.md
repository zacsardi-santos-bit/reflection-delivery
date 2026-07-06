I'm working with the Python bindings for an inter-process communication library that supports event-based messaging. The library already lets me create and open event services from Python, but I can't actually use them because the notifier and listener port types are missing from the Python API entirely.

I need to be able to create a notifier from an event service and use it to send notifications — either using a default event identifier I configure upfront, or a custom one I supply at send time. On the other side, I need a listener port that can receive those notifications in different modes: non-blocking (try to get one event now), time-limited (wait up to a given duration for one event), and fully blocking (wait indefinitely for one event). I also need variants of each that collect all currently pending events at once rather than just the next one.

The listener and notifier should also expose the deadline duration configured on the service so that applications can read it at runtime from either port.

Events should be returned to the listener in the order they were sent.

Could you implement these missing port types and wire them up so they are accessible from the Python API?
