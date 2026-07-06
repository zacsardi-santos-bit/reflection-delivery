## Description

Project-scoped secrets stored in Rancher's management namespace are labeled to identify which project they belong to, but they carry no label indicating which cluster they originate from. This means there is no way to determine cluster membership by inspecting the secret's labels alone, which creates problems when managing secrets across multiple clusters.

## Expected Behavior

- Project-scoped secrets should be labeled with both their project name and their cluster name so that cluster membership can be determined directly from a secret's labels.
- A new operation should be available to scan existing project-scoped secrets and add the missing cluster label to any that do not already have it, working in a non-blocking manner so that failures on individual secrets do not prevent the rest from being updated.
- The existing migration operation that handles secrets left over from a previous controller should also stamp the cluster label on secrets it processes.

## Why This Matters

Without a cluster label, operators and automated tooling cannot easily filter or identify which cluster a project-scoped secret belongs to. This gap also complicates future multi-cluster secret management scenarios. Adding the cluster label — and backfilling it onto existing secrets — ensures all project-scoped secrets are consistently and fully labeled.
