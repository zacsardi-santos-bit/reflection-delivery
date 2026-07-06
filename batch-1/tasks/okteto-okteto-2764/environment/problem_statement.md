## Description

When a deployment fails during the build phase (for example, because a Dockerfile is missing or invalid), the pipeline's state in the cluster is not updated to reflect the failure. This means monitoring tools, dashboards, and integrations that watch the pipeline's configmap will still see the last known status rather than the actual error state. The error is returned to the caller, but the cluster-side state is out of sync.

## Expected Behavior

- When the deploy command starts, it should create a configmap to track the pipeline's progress, populated with deployment metadata (name, namespace, repository, branch, filename, icon, manifest content).
- If the build step fails during deployment, the pipeline configmap should be updated to "error" status before the error propagates.
- The build error message should be written to the log output buffer so it can be observed by users and integrations.
- Specifically, build failures caused by invalid or missing Dockerfiles should produce a consistent, clearly identifiable error message.

## Why This Matters

Operators and CI/CD integrations rely on the pipeline configmap to track deployment state. When a build fails silently (from the configmap's perspective), it becomes difficult to diagnose failures or trigger automated recovery. Properly updating the configmap on build failure ensures the entire system accurately reflects what happened and allows tools to react accordingly.
