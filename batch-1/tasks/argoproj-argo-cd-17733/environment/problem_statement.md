## Description

Argo CD's "ignore differences" feature is broken when the ignore rules target fields inside arrays or lists within a resource. When a user configures path-based ignore rules that select elements within an array — for example, individual routes in an ingress resource or specific environment variables inside a container — the normalization step that reconciles live and target state produces corrupted output: array entries that should contain real data are replaced with empty objects. Additionally, when the target manifest has added new array entries that aren't in the live resource, those new entries may be dropped or mishandled.

## Expected Behavior

- When an ignore rule targets array elements, the normalization should preserve the full content of the array entries from the live resource (not replace them with empty objects).
- When the target manifest has new array entries (e.g., new environment variables added to a container) alongside an ignored entry, all entries should appear in the normalized result — the ignored entry with its live value, and the new entries from the target.
- When combined ignore rules (both field-level pointers and path expressions) are applied to a resource that has gained additional fields in the target (new labels, new resource requests, new env vars), the normalized result should include those additional target fields while keeping the live value for the ignored fields.

## Why This Matters

Users rely on ignore-difference rules to manage fields that are modified automatically by the cluster (e.g., runtime-injected environment variables, auto-assigned replicas). When these rules target array elements, the current behavior can cause resources to appear perpetually out-of-sync, and syncing may overwrite live data with corrupted empty objects rather than leaving those fields untouched.
