## Description

When deploying a static pod (such as the Kubernetes API server) on a node, the operator needs to retrieve the associated configuration resources — certificates and config files stored as secrets and configmaps — from the Kubernetes API and write them to the local filesystem so the pod can reference them at runtime. Currently, there is no automated mechanism to do this for a specific deployment revision; the relevant resources must be manually copied or are simply not available on disk when the pod starts.

## Expected Behavior

- Given a deployment revision identifier, a namespace, and lists of secret and configmap name prefixes, the installer should retrieve each corresponding resource (named by concatenating the prefix and the revision identifier) from the Kubernetes API.
- Secret contents should be written to a structured directory under the resource directory, organized by resource type and resource name.
- Configmap contents should similarly be written to the correct subdirectory.
- The pod manifest (stored in a dedicated configmap) should be written both into the versioned resource directory and into the pod manifest directory where the static pod manager will discover it.

## Why This Matters

Without this capability, deploying a new revision of a static pod requires manual intervention to place configuration files on disk. Automating this step is essential for reliable, revision-controlled rollouts of the static pod by the operator.
