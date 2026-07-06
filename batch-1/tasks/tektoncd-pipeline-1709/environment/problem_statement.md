## Description

The TaskRun reconciler currently tracks which pod is running a task by storing the pod's name in the TaskRun's status. This is fragile: if the stored pod name is missing or gets out of sync, the reconciler cannot find or manage the pod.

We should instead use labels to associate pods with their TaskRun. Each pod created for a TaskRun should be labeled with the TaskRun's name, so the reconciler can always find the pod via a label query — even if the status field is missing.

## Expected Behavior

- Pods created for a TaskRun are labeled with the TaskRun's name using the standard Tekton label key for TaskRun ownership.
- The reconciler discovers the pod for a TaskRun by querying pods with that label, using a list operation, rather than a direct name-based lookup.
- When a TaskRun is cancelled, the reconciler sets the failure condition on the TaskRun **and** deletes the associated pod (found via label).
- If no pod exists yet for a cancelled TaskRun, the reconciler still sets the cancelled condition and returns successfully.
- If the pod list operation fails, the reconciler surfaces that error.

## Why This Matters

Storing the pod name in the TaskRun status creates an implicit dependency on that field remaining correct. Label-based discovery is more resilient and follows Kubernetes conventions for ownership. Additionally, the current cancel logic only updates the TaskRun's condition but does not clean up the running pod, leaving orphaned pods behind when a user cancels a task.
