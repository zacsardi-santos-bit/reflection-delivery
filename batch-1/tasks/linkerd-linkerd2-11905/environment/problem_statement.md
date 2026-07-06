## Description

The Linkerd destination controller needs to manage endpoint slices for external workloads — workloads running outside the Kubernetes cluster that are registered via a custom resource. When an external workload's configuration is updated, the controller must determine which services are affected and queue only those for reconciliation, rather than doing a full sweep each time.

Currently, there is no mechanism to compare two versions of an external workload and determine whether the change is significant enough to warrant reconciliation, or to identify exactly which services need to be updated. Without this, the controller would either skip necessary updates or trigger unnecessary work.

## Expected Behavior

- A function that compares two versions of an external workload and detects whether the spec (IP addresses and port configuration) has materially changed
- A method that, given an old and updated workload, returns the minimal set of services needing reconciliation:
  - When nothing changed: return an empty result
  - When only the workload's network configuration changed: return the services currently selecting the workload
  - When only the workload's labels changed: return services that gained or lost a match (but not services that matched both before and after, since those are unaffected)
  - When both labels and configuration changed: return the union of services matching either the old or new version

## Why This Matters

This logic is essential for an efficient endpoints controller: it prevents unnecessary reconciliation churn while guaranteeing that all services whose endpoint slices are stale will be queued for update. It also requires the supporting fake API infrastructure to handle the external workload resource type so the controller can be properly tested.
