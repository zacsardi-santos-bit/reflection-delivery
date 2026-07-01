## Description

When the operator reconciles a service account, it inadvertently removes image pull secrets that were added by the platform or other controllers. On certain platforms (such as OpenShift), the platform automatically injects image pull secrets into every service account. When the operator then updates that service account without those secrets in its desired state, the platform-injected secrets are stripped out.

This causes a reconciliation loop: the operator removes the image pull secrets, the platform controller re-adds them, which triggers the operator to reconcile again, which removes them again, and so on — indefinitely.

## Expected Behavior

- When updating a service account, the operator should check whether the existing object has image pull secrets that are not present in the desired state.
- If the existing service account has image pull secrets and the desired state has none, the existing image pull secrets should be carried forward into the update, preserving them.
- This behavior should mirror the existing logic that already preserves regular secrets during service account updates.

## Why This Matters

Without this fix, environments where the platform injects image pull secrets into service accounts experience a continuous, unnecessary reconciliation loop. This wastes resources and can interfere with stable cluster operation. Preserving externally-managed image pull secrets prevents the loop and ensures the operator plays well with other controllers managing service account data.
