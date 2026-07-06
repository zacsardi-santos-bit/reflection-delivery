Implement a mechanism to prevent duplicate state transitions in flow runs by using unique identifiers. Ensure that each state transition request includes a unique identifier to detect retries, and modify the server to reject duplicate transitions based on these identifiers.

*   Create a class named `PreventDuplicateTransitions` in `src/prefect/server/orchestration/core_policy.py`.
    *   Ensure it is a subclass of `BaseOrchestrationRule`.
    *   Implement it as a `before_transition` hook for all state transitions.
    *   Reject transitions with the reason 'This run has already made this state transition.' if both current and proposed states have matching `transition_id`.
    *   Return the existing state if a transition is rejected.
    *   Accept transitions if either state lacks a `transition_id` or if the IDs differ.
    *   Register in `CoreFlowPolicy.priority()` for evaluation during all flow run state transitions.

*   Update the `StateDetails` model:
    *   In `src/prefect/server/schemas/states.py`, add an optional `transition_id` field of type `UUID`, defaulting to `None`.
    *   In `src/prefect/client/schemas/objects.py`, add a similar `transition_id` field.

*   Modify the `set_flow_run_state` method in `PrefectClient` located in `src/prefect/client/orchestration.py`:
    *   Automatically generate a new unique `UUID` for `transition_id` in `state_create.state_details` before sending any request.

*   Update the API endpoint `POST /flow_runs/{id}/set_state`:
    *   Return HTTP 200 with REJECT status and reason 'This run has already made this state transition.' when a duplicate transition is detected.
    *   Include the existing state in the response for duplicate transitions.
    *   Maintain existing behavior for invalid transitions, such as PENDING-to-PENDING, when no duplicate `transition_id` is involved.

*   Ensure that when a worker sets the state on a flow run, the `state_details` field reflects the newly generated `transition_id`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.