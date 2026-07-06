Implement logic to handle managed placement group deletions in the cluster update policy system. Ensure that deletions of cluster-managed placement groups are blocked if compute nodes are running, and allow them if all nodes are stopped. Distinguish between managed and user-provided placement groups and prioritize compute-resource-level configurations.

*   Implement `is_managed_placement_group_deletion(change, patch)` in `cli/src/pcluster/config/update_policy.py`:
    *   Return `True` if the effective placement group in the base config is managed (enabled with no Name or Id) and the target config is not managed.
    *   Return `False` if the base config has an explicit Name or Id, or if a managed placement group is being created.
    *   Determine the effective placement group by checking the compute-resource-level first, then the queue-level if necessary.
    *   Parse `change.path` entries like 'Queues[queue-name]' and 'ComputeResources[cr-name]' to find the relevant configurations in `base_config` and `target_config` under the 'Scheduling' key.

*   Implement `condition_checker_managed_placement_group(change, patch)` in `cli/src/pcluster/config/update_policy.py`:
    *   Return `False` if `is_managed_placement_group_deletion(change, patch)` is `True` and `patch.cluster.has_running_capacity()` is `True`.
    *   Return `True` if `is_managed_placement_group_deletion(change, patch)` is `True` and the cluster has no running capacity.
    *   Delegate to the queue update strategy condition checker if `is_managed_placement_group_deletion(change, patch)` is `False`.

*   Implement `fail_reason_managed_placement_group(change, patch)` in `cli/src/pcluster/config/update_policy.py`:
    *   Return "All compute nodes must be stopped for a managed placement group deletion" if `is_managed_placement_group_deletion(change, patch)` is `True`.
    *   Return "All compute nodes must be stopped" if `is_managed_placement_group_deletion(change, patch)` is `False`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.