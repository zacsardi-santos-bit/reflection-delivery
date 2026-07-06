## Description

We need a new script in the Cortex REST API pack that retrieves and filters the tasks from a specific incident's playbook execution. The existing script in this pack only supports filtering tasks by state, but users often need to find tasks by name or tag as well. Additionally, the existing script does not handle nested sub-playbooks — tasks that run inside a sub-playbook are invisible to it.

## Expected Behavior

- Users should be able to query all tasks for a given incident, optionally filtering by task name, task tag, and/or task state.
- When no state filter is provided, all tasks regardless of state should be returned.
- When filtering by the generic "error" state, both error and loop-error tasks should be included.
- Tasks nested inside sub-playbooks should be traversed and included in the results, not just the top-level tasks.
- The output should include a structured list of task objects with relevant fields (id, name, type, owner, state, script reference, dates, parent playbook reference, and who completed the task).
- A formatted human-readable table should be produced summarizing matching tasks.
- The older state-only script should be deprecated in favor of this new one.

## Why This Matters

Incident responders frequently need to inspect specific tasks within a complex playbook to understand what ran, what failed, or who completed a step. Without name and tag filtering, they must manually sift through all tasks. Without sub-playbook traversal, nested tasks are silently omitted, giving an incomplete picture of playbook execution.
