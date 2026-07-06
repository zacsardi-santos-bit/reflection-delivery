## Description

Slurm clusters report GPU resources using raw GRES (Generic Resource) strings that vary widely in format — vendor prefixes, different separator conventions, architecture suffixes, and memory variant labels are all mixed together. When a user requests a specific GPU type, there's no reliable way to match that request against the cluster's actual available resources, leading to incorrect "not available" errors or mismatches between requested and allocated GPU types.

Additionally, the canonical list of GPU names that is already used for matching in other backends (such as Kubernetes) is buried in a backend-specific module, preventing its reuse across different cluster adapters.

## Expected Behavior

- A GPU name normalization utility should strip vendor prefixes and standardize separator characters so that raw GRES strings can be compared uniformly.
- A matching function should be able to determine whether a user-requested GPU type corresponds to a given raw GRES string using subsequence matching to handle variants, while correctly distinguishing similar names (e.g., L4 should not match L40 nodes).
- A resolution function should look up all nodes in a cluster (optionally filtered by partition and minimum GPU count), find the actual GRES identifier that matches the requested GPU type, and handle cases where multiple candidate GRES types match by preferring exact matches, then the type available on the most nodes, then alphabetical ordering.
- A canonicalization function should convert raw GRES strings back to the well-known canonical GPU names used across the system, with a sensible uppercase fallback for unrecognized types.
- The canonical GPU name list should be moved to a shared utility module so it is accessible to both Kubernetes and Slurm backends.
- When no matching GPU is found, error messages should indicate what GPU types are actually available on the cluster. The error should distinguish between the case where no GPU nodes exist at all versus the case where GPU nodes exist but none match the requested type.

## Why This Matters

Without accurate GPU name resolution, users on Slurm clusters may be denied resources that are actually available, or have the wrong GPU allocated to their jobs. Centralizing the canonical name list also prevents duplication and keeps GPU identification consistent across cloud backends.
