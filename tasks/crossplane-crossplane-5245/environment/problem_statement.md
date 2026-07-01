## Description

There is currently no built-in way to inspect the CPU and memory consumption of Crossplane-managed pods directly from the Crossplane CLI. Operators have to rely on generic Kubernetes tooling and manually identify which pods belong to Crossplane components (the core controller, providers, functions, and extensions). This is tedious and error-prone, especially in environments with many workloads.

## Expected Behavior

- A new CLI subcommand should display resource usage (CPU and memory) for all Crossplane-related pods in a given namespace.
- The output should be a cleanly formatted table with columns for pod type, namespace, name, CPU usage (in millicores), and memory usage (in mebibytes).
- An optional summary mode should aggregate the results: total pod count, per-type counts, total CPU, and total memory.
- Pods should be classified by type (e.g., provider, function, the core controller, or any future extension type) based on their labels.
- The summary should list pod types alphabetically with the type name's first letter capitalized.

## Why This Matters

Crossplane operators need a quick and focused way to understand the resource footprint of their Crossplane installation. Without this, diagnosing performance issues or planning capacity requires manually sifting through all cluster pods. A dedicated resource-usage subcommand makes day-to-day operations significantly easier.
