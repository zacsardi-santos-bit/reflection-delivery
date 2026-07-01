## Description

Kafka Connect workers in distributed mode are not handling broker coordinator unavailability gracefully. When the broker temporarily goes offline, workers wait indefinitely to reconnect to the group coordinator. This means that when the broker comes back, tasks may not automatically resume, and workers can end up in an inconsistent state where they continue running tasks despite no longer being active group members.

Additionally, the incremental cooperative rebalance assignor does not currently incorporate the worker's current group membership identity (generation and member information) when computing assignments, making it harder to correlate assignments with their originating rebalance.

## Expected Behavior

- When the broker coordinator becomes unreachable, a Connect worker should detect this within a bounded timeout and gracefully revoke its current assignment instead of blocking indefinitely.
- When the broker comes back online on the same ports, workers should automatically rejoin the group and connector tasks should return to a running state without manual intervention.
- Failed connector tasks should be restartable through the REST interface — after reconfiguring the connector with valid settings, posting a restart request to the task endpoint should bring the task back to a running state.
- The distributed rebalance assignor should include the current group generation and member identity when performing task assignment, so each assignment can be traced to the correct rebalance generation.

## Why This Matters

Without these fixes, a temporary broker outage can leave Kafka Connect in a broken state that requires manual recovery. Operators expect Connect workers to self-heal after transient broker failures, and tasks should automatically resume once the broker is available again.
