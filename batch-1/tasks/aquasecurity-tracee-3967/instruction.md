Enhance the event dependencies manager to track probe dependencies, support event activation cancellation, and improve error handling. Update subscriber callbacks to influence node addition and removal outcomes. Implement shared test utilities for policy and logging management.

Requirements:

*   Update the dependencies manager:
    *   Implement probe dependency tracking.
        *   Add `GetProbe(handle probes.Handle) (*ProbeNode, error)` to retrieve probe nodes.
        *   Ensure `ProbeNode` exposes `GetDependents()` for dependent event IDs.
    *   Implement event activation cancellation.
        *   Modify `SelectEvent(id events.ID) (*EventNode, error)` to return `*ErrNodeAddCancelled` on cancellation.
        *   Roll back partially added events on cancellation.
    *   Improve error handling.
        *   Change `GetEvent(id events.ID) (*EventNode, error)` to return `ErrNodeNotFound`.
        *   Change `RemoveEvent(id events.ID) error` to return `ErrNodeNotFound` if the event is absent.
    *   Update subscriber callback signatures.
        *   Modify `SubscribeAdd` and `SubscribeRemove` to accept a `NodeType` and an action-returning callback.

*   Implement new types and constants:
    *   Define `EventNodeType` as a `NodeType` constant with value "event".
    *   Define `ErrNodeNotFound` as a sentinel error.
    *   Define `Action` as an empty interface.
    *   Define `CancelNodeAddAction` with a `Reason` field.
    *   Define `ErrNodeAddCancelled` with a `Reasons` field.
    *   Add `NewCancelNodeAddAction(reason error) *CancelNodeAddAction`.

*   Implement new methods and functions:
    *   Add `NewProbe(handle probes.Handle, required bool) Probe` in `pkg/events`.
    *   Add `GetLogger() LoggerInterface` in `pkg/logger`.

*   Update test utilities in `tests/testutils`:
    *   Add `PolicyFileWithID` struct.
    *   Add `NewPolicies` and `BuildPoliciesFromEvents` functions.
    *   Add `SetTestLogger` and `TestLogs` functions.

*   Modify integration test structures:
    *   Rename `evts` to `expectedEvents` and add `unexpectedEvents` in `cmdEvents`.
    *   Ensure `ExpectAtLeastOneForEach` checks for presence and absence of events.

*   Add new event ID constants in `pkg/events`:
    *   `ExecTest` with value 8000.
    *   `MissingKsymbol` with value 8001.
    *   `FailedAttach` with value 8002.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.