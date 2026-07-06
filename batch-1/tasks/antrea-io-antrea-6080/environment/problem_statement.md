## Description

The current self-signed certificate rotation logic for the Antrea controller is based on a fixed maximum duration. This means certificates get rotated on a timer regardless of how much validity remains — which is both wasteful and imprecise. We need a smarter rotation strategy: rotate certificates only when the remaining validity drops below a configurable minimum threshold, rather than after a fixed elapsed time.

Additionally, the current implementation regenerates a fresh certificate every time the controller restarts, even if a perfectly valid certificate already exists. This causes unnecessary disruption for clients that have already established trust with the existing certificate. The new approach should persist the generated certificate to a Kubernetes Secret and reuse it on subsequent starts, as long as it still has sufficient validity remaining. Multiple controller instances running simultaneously (e.g., during a rolling deployment) should converge to the same certificate rather than each independently generating their own.

## Expected Behavior

- The certificate configuration should define a **minimum valid duration** field (how much time must remain before triggering rotation), replacing the old maximum rotation duration field.
- Self-signed certificates should be persisted to a Kubernetes Secret and loaded from it on startup.
- Rotation should be triggered when the certificate's remaining validity falls below the minimum valid duration, not after a fixed interval.
- If the certificate in the Secret is invalid or missing, a new one should be generated and saved.
- When the Secret is updated externally (e.g., by another controller instance), the running controller should pick up the change and update its serving certificate accordingly.
- The multicluster controller configuration should reflect the new field, set to 90 days.

## Why This Matters

This change eliminates unnecessary certificate churn during controller restarts and rolling deployments, and gives operators a more meaningful way to control when certificates are renewed — based on remaining validity rather than elapsed time.
