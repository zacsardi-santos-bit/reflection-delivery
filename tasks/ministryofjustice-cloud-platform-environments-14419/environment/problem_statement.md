## Description

The activities-api-dev namespace currently includes a configuration file that sets up AWS IAM role bindings for its service accounts. This integration allows the namespace's workloads to access certain cloud message queues and notification topics via an assumed AWS role. However, this access is no longer required for this environment, and the configuration should be removed.

## Expected Behavior

- The AWS IAM role binding configuration file for the activities-api-dev namespace should be deleted from the repository.
- The namespace should no longer have an associated AWS role or the corresponding service account annotations that were created by that configuration.

## Why This Matters

Leaving unused cloud infrastructure configuration in place creates unnecessary AWS resources, potential unintended access, and maintenance overhead. Removing the file ensures the namespace is clean and no longer provisions IAM role infrastructure that is no longer needed.
