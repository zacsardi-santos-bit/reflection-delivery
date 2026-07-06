I'm working on the event dependencies manager in the tracee project and need help with several related improvements.

Right now the manager tracks event-to-event dependencies, but it doesn't track which kernel probes an event depends on. I need it to track probe dependencies so that when events are deactivated the system knows which probes are still in use and which can be cleaned up. There should be a way to look up a probe by its handle and find out which events currently depend on it.

I also need to add cancellation support to the event activation flow. When an event is being added along with its dependencies, a subscriber should be able to return a signal that aborts the operation. If cancelled, the manager should roll back everything that was added during that call — removing all the partially-added events and firing the appropriate removal callbacks — and then return a specific error type so the caller knows the add was cancelled. This is important for graceful handling of situations like a missing kernel symbol or a probe that can't be attached.

The current API uses boolean returns for lookups (event exists? yes/no), which doesn't fit Go conventions well and makes it hard to distinguish different failure modes. I'd like to change the lookup methods and the remove method to return proper errors, using a sentinel "not found" error that callers can check with the standard errors package.

The subscriber callback signature also needs to change: instead of being a void function called purely for side effects, callbacks should return a slice of actions so they can influence the outcome of the operation.

I also need to add some shared test utilities to a common package so they can be reused across different test files: utilities for building policies from event IDs, for capturing log output to a channel during tests, and for checking that specific log messages appear during a test run. There's also a small constant that should be named rather than using a raw number in the policy validation code.
