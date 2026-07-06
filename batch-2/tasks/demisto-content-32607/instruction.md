Implement a new script in the Cortex REST API pack to retrieve and filter tasks from a specific incident's playbook execution. Ensure the script supports filtering by task name, tag, and/or state, and handles nested sub-playbooks. Deprecate the existing state-only filtering script in favor of this new one.

*   Implement the `is_task_match` function:
    *   Accept parameters: `task` (dict), `name` (str or None), `tag` (str or None), and `states` (list).
    *   Return `True` if the task matches the provided `name` (case-insensitive), contains the `tag`, and its state is in `states`.
    *   Return `True` if `states` is empty, indicating no state filtering.
    *   Return `False` if any provided filter does not match.

*   Implement the `get_states` function:
    *   Accept a list of state strings.
    *   Return all possible states in this order when the list is empty: `["", "inprogress", "Completed", "Waiting", "Error", "LoopError", "WillNotBeExecuted", "Blocked"]`.
    *   Return `["Error", "LoopError"]` if the list contains "error".
    *   Otherwise, return the resolved state strings corresponding to the input values.

*   Implement the `get_playbook_tasks` function:
    *   Accept a list of task dicts.
    *   Return a flattened list including all nested sub-playbook tasks.
    *   Insert nested tasks before the parent task in the result list.
    *   Return an empty list for empty input.

*   Implement the `get_task_command` function:
    *   Accept an `args` dict with at least 'inc_id', optionally 'name', 'tag', and 'states'.
    *   Return a `CommandResults` object with:
        *   `outputs_key_field` set to "id".
        *   `outputs` as a list of task dicts with keys: id, name, type, owner, state, scriptId, startDate, dueDate, completedDate, parentPlaybookID, completedBy.
        *   `readable_output` as a markdown table titled "Incident #{inc_id} Playbook Tasks" with columns id, name, and state (omit columns with all-null values).

*   Create a test data file at `Packs/DemistoRESTAPI/Scripts/GetIncidentTasks/test_data/core-api-response.json`:
    *   Structure as a JSON array with one element containing a "Contents" object.
    *   Include a nested path "response" > "invPlaybook" > "tasks" with a dict of task objects keyed by string IDs.
    *   Include tasks: "First Task" (state "Completed", type "regular", tags ["testtag"]), "Second Task" (state "Completed", type "regular", empty tags), and a playbook task with a subPlaybook containing "Sub-playbook Tasks".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.