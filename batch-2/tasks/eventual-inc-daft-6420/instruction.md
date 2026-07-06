I'm working with Daft's subscriber system and I'd like a way to automatically capture query lifecycle events to disk so I can review them later.

*   The EventLogSubscriber class must accept a directory path in its constructor, create the directory if it does not exist, initialize _closed to False, and initialize four internal timing dictionaries: _query_starts (keyed by query_id string), _optimization_starts (keyed by query_id string), _exec_starts (keyed by query_id string), and _operator_starts (keyed by (query_id, node_id) tuples).

*   When close() is called without any prior query activity, no events.jsonl files must be created anywhere under the log directory.

*   When on_query_start is called, the subscriber must create a file at <log_dir>/<query_id>/events.jsonl and write three events in order: 'event_log_started', 'query_started', and 'plan_unoptimized'. Each event must be a JSON object on its own line with at minimum an 'event' field, a 'ts' field, and a 'query_id' field.

*   When on_query_end is called for a finished query, the subscriber must write a 'query_ended' event that includes 'query_id', 'status' set to 'ok', and 'duration_ms' as a non-negative integer. For a failed query the 'status' must be 'failed'. For a canceled query the 'status' must be 'canceled'.

*   After on_query_end is called for a query, the query_id must be removed from _query_starts, _optimization_starts, _exec_starts, and all (query_id, node_id) entries from _operator_starts.

*   When on_query_end is called for one query while other queries are still active, only the ended query's state must be cleared; the active queries' entries in _query_starts, _optimization_starts, _exec_starts, and _operator_starts must remain unchanged.

*   The close() method must set _closed to True and close all open file handles.

*   The enable_event_log function must accept an optional log directory path, create and attach an EventLogSubscriber to the Daft context using _EVENT_LOG_ALIAS as the subscriber identifier, and store the subscriber in the module-level _EVENT_LOG_SUBSCRIBER variable.

*   The disable_event_log function must set _EVENT_LOG_SUBSCRIBER to None, call close() on the subscriber (resulting in _closed being True), and detach the subscriber from the Daft context such that any subsequent attempt to detach the same alias raises an exception.

*   The module must export _EVENT_LOG_ALIAS as a string constant and _EVENT_LOG_SUBSCRIBER as a module-level variable (initially None) accessible from outside the module.


*   Interface details: Type: Class
Name: EventLogSubscriber
Location: daft/subscribers/events.py
Description: A subscriber that writes query lifecycle events to JSONL log files, one file per query, under a configurable log directory. Extends the Subscriber base class from daft/subscribers/abc.py.
Signature:
  __init__(self, log_dir: str | Path) -> None
  close(self) -> None
  on_query_start(self, query_id: str, metadata: PyQueryMetadata) -> None
  on_query_end(self, query_id: str, result: PyQueryResult) -> None
  on_optimization_start(self, query_id: str) -> None
  on_exec_start(self, query_id: str, physical_plan: str) -> None
  on_exec_operator_start(self, query_id: str, node_id: int) -> None

Internal state attributes (accessed by tests):
  _closed: bool  — set to False on init, set to True by close()
  _query_starts: dict[str, float]  — maps query_id to monotonic start time in ms
  _optimization_starts: dict[str, float]  — maps query_id to optimization start time in ms
  _exec_starts: dict[str, float]  — maps query_id to execution start time in ms
  _operator_starts: dict[tuple[str, int], float]  — maps (query_id, node_id) to operator start time in ms; tests iterate keys as (qid, _) tuple pairs

Event log file location: <log_dir>/<query_id>/events.jsonl
Each event line is a JSON object with at minimum: {"event": <name>, "ts": <iso timestamp>, "query_id": <query_id>, ...}

Events written by on_query_start in order: "event_log_started", "query_started", "plan_unoptimized"
Events written by on_query_end: "query_ended" with fields "query_id", "status" ("ok" | "failed" | "canceled"), and "duration_ms" (non-negative integer)


Type: Function
Name: enable_event_log
Location: daft/subscribers/events.py
Signature: enable_event_log(log_dir: str | Path | None = None) -> None
Description: Creates an EventLogSubscriber for the given directory (or a default location if None), attaches it to the Daft context using _EVENT_LOG_ALIAS, and stores it in the module-level _EVENT_LOG_SUBSCRIBER variable.


Type: Function
Name: disable_event_log
Location: daft/subscribers/events.py
Signature: disable_event_log() -> None
Description: Sets the module-level _EVENT_LOG_SUBSCRIBER to None, detaches the subscriber from the Daft context using _EVENT_LOG_ALIAS, and calls close() on the subscriber. After this call, attempting to detach _EVENT_LOG_ALIAS again from the context must raise an exception.


Type: Module-level constant
Name: _EVENT_LOG_ALIAS
Location: daft/subscribers/events.py
Description: A string constant used as the subscriber identifier when attaching/detaching from the Daft context. Imported directly by tests.


Type: Module-level variable
Name: _EVENT_LOG_SUBSCRIBER
Location: daft/subscribers/events.py
Description: Holds the currently active EventLogSubscriber instance, or None if event logging is not enabled. Set by enable_event_log and cleared to None by disable_event_log. Accessed directly by tests as subscriber_events._EVENT_LOG_SUBSCRIBER.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.