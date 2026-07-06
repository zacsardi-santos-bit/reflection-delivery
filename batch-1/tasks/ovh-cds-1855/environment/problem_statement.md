## Description

When a user tries to re-run a specific node in a workflow, the system needs to determine whether that node is actually eligible to be re-triggered. Currently, this eligibility check is either missing or based on an overly simplified heuristic that does not consider the actual state of upstream nodes in the workflow graph.

## Expected Behavior

- A workflow node should only be re-runnable if all of its ancestor nodes (those that trigger it, directly or indirectly) have already completed execution — meaning their runs have reached a terminal state such as success or failure.
- If any ancestor node is still in progress (e.g., currently building or waiting), the node should not be eligible for re-triggering.
- If an ancestor node has never been built (empty status), the node should also not be eligible.
- If a node has no ancestors (e.g., it is the root node of the workflow), it should be eligible as long as its own status is terminal.

## Why This Matters

Without this logic, users could attempt to re-run a node whose upstream dependencies have not finished, potentially causing race conditions or inconsistent pipeline state. Having accurate eligibility information allows the UI and API to correctly enable or disable the re-run action for each individual workflow node, providing a better and safer user experience.
