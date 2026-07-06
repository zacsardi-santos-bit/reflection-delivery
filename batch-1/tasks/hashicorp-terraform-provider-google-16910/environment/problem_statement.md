## Description

There is a persistent diff (permadiff) that occurs on `google_compute_instance_group_manager` and `google_compute_region_instance_group_manager` resources when using stateful external or internal IP configurations. After applying a configuration with multiple stateful IP blocks, running `terraform plan` again shows a diff even though no changes were made. This happens because the Google Cloud API returns stateful IPs in alphabetical order by interface name, which may differ from the order specified in the Terraform configuration.

## Expected Behavior

- After applying a configuration with stateful external or internal IP blocks in a specific order, a subsequent `terraform plan` should show no diff.
- The IP blocks should be preserved in the same order as specified in the user's configuration.
- If the API returns IPs not present in the configuration, they should be appended to the end in a stable (alphabetical) order.
- If the API does not return an IP that was in the configuration, it should be omitted from the state.

## Why This Matters

Users who define stateful IP configurations in a non-alphabetical order will see Terraform constantly report changes on those resources even when the infrastructure is already in the desired state. This makes it impossible to confirm that no real changes are needed and forces users to accept spurious diffs on every plan/apply cycle.
