## Description

When installing provider components using the cluster management tooling, the tool processes provider manifests and rewrites namespace references so they point to the user's chosen target namespace. Currently, this rewriting logic handles regular objects and their metadata namespaces, but it does **not** handle webhook configurations or custom resource definitions that contain embedded namespace references.

As a result, providers that include admission webhooks or CRD conversion webhooks end up deployed with incorrect namespace references — their webhook service namespaces and cert-manager certificate injection annotations still point to the original (hardcoded) namespace rather than the target namespace. This causes those components to malfunction or fail entirely after installation.

## Expected Behavior

- Admission webhook configuration objects should have the namespace inside their webhook service client configuration updated to the target namespace.
- Custom resource definition objects that use webhook-based conversion should have the namespace inside their conversion webhook service client configuration updated to the target namespace.
- Any cert-manager certificate injection annotation that references a namespace (in the format `namespace/cert-name`) should have its namespace portion updated to the target namespace.
- The function responsible for these namespace updates should also propagate errors rather than silently ignoring them.

## Why This Matters

Providers that rely on webhooks (which is increasingly common) cannot be installed into a custom namespace using this tooling. Fixing this ensures the tool works correctly for the full range of provider manifests in use today.
