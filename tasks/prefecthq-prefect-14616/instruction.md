Refactor the event processing system to separate the causal ordering logic into its own module. Implement a scope-aware causal ordering system that allows multiple independent instances without shared state. Ensure the triggers module can retrieve and interact with the shared ordering instance.

*   Create a new module at `src/prefect/server/events/ordering.py` that exports:
    *   `CausalOrdering` class
    *   `EventArrivedEarly` exception
    *   `MaxDepthExceeded` exception
    *   `MAX_DEPTH_OF_PRECEDING_EVENT` constant
    *   `PRECEDING_EVENT_LOOKBACK` constant

*   Implement the `CausalOrdering` class with the following specifications:
    *   Constructor: `__init__(self, scope: str) -> None`
        *   Initialize with a `scope` parameter to ensure independent state per instance.
    *   Method: `preceding_event_confirmed(self, handler, event: ReceivedEvent, depth: int = 0) -> AsyncContextManager`
        *   If the event's `follows` field points to an unseen event within `PRECEDING_EVENT_LOOKBACK`, record as a follower and raise `EventArrivedEarly`.
        *   Allow immediate processing if outside the lookback window.
        *   After execution, mark the event as seen and recursively evaluate followers.
        *   Raise `MaxDepthExceeded` if depth exceeds `MAX_DEPTH_OF_PRECEDING_EVENT`.
    *   Method: `async record_event_as_seen(self, event: ReceivedEvent) -> None`
        *   Mark an event as seen within the scope.
    *   Method: `async event_has_been_seen(self, event: ReceivedEvent) -> bool`
        *   Return `True` if the event has been recorded as seen within the scope.
    *   Method: `async record_follower(self, event: ReceivedEvent) -> None`
        *   Record an event as a follower within the scope.
    *   Method: `async get_followers(self, event: ReceivedEvent) -> list[ReceivedEvent]`
        *   Retrieve followers of an event within the scope.
    *   Method: `async forget_follower(self, event: ReceivedEvent) -> None`
        *   Remove an event from its predecessor's follower list within the scope.
    *   Method: `async get_lost_followers(self) -> list[ReceivedEvent]`
        *   Return events waiting for predecessors outside the lookback window, ordered by `occurred` timestamp.

*   In `src/prefect/server/events/triggers.py`, implement:
    *   Function: `causal_ordering() -> CausalOrdering`
        *   Return the shared `CausalOrdering` instance used by the triggers module.
        *   Ensure multiple calls with the same scope return objects sharing the same event-seen state.

*   Ensure `EventArrivedEarly` is accessible as `triggers.EventArrivedEarly`.

*   Ensure `PRECEDING_EVENT_LOOKBACK` is located at `prefect.server.events.ordering.PRECEDING_EVENT_LOOKBACK` for patching.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.