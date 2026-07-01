## Description

We need a new lint check for StatefulSet resources that validates whether each volume claim template carries a required annotation. Currently there is no way to enforce annotation-based policies on the persistent storage that StatefulSets provision, making it easy for teams to accidentally deploy StatefulSets whose storage volumes lack required metadata (e.g., backup policy labels, billing tags, compliance markers).

## Expected Behavior

- A configurable linting template should allow users to specify which annotation key is required on every volume claim template within a StatefulSet.
- If any volume claim template is missing that annotation — whether it has other annotations or none at all — the linter should report a diagnostic identifying the missing requirement.
- If all volume claim templates carry the required annotation, no diagnostic should be produced.
- The diagnostic message should clearly name the StatefulSet resource type and specify which annotation was expected.
- The template should validate that the annotation parameter is provided at configuration time, rejecting configurations that omit it.
- The linting infrastructure should also expose a way to retrieve the API version for PersistentVolumeClaim resources.

## Why This Matters

Teams managing Kubernetes clusters at scale rely on annotations to enforce operational policies on storage volumes. Without an automated check, non-compliant StatefulSets can slip into production undetected, creating compliance gaps or causing operational issues such as missed backups or untracked cloud costs.
