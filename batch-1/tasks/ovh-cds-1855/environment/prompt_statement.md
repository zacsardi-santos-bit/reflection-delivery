I'm working on a workflow execution engine and need to add logic to determine whether a particular workflow node run is eligible to be re-triggered. Right now there's no proper check for this — the system doesn't look at the state of upstream nodes in the workflow graph before deciding whether a node can be re-run.

The logic should be: a node run can only be re-triggered if all of its ancestor nodes (the nodes that feed into it via triggers) have already reached a terminal state. If an ancestor is still in progress or has never been run (empty status), the node should not be considered runnable. If the node has no ancestors, it should be runnable as long as its own current state is terminal.

This function should live in the workflow data access layer alongside other workflow run loading code.
