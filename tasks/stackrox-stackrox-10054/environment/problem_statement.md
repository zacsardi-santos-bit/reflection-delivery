## Description

A built-in security policy that checks for missing container resource settings needs to be renamed and its detection criteria narrowed. The current policy flags containers that are missing any of four resource settings (CPU requests, CPU limits, memory requests, and memory limits). This produces unnecessary alerts for workloads that intentionally omit certain settings.

## Expected Behavior

- The policy should be renamed to accurately reflect that it only checks for two specific resource settings: the CPU request and the memory limit.
- The policy's violation criteria should be narrowed so that it only alerts when a CPU request or a memory limit is missing — not when a CPU limit or memory request is absent.
- Existing deployments should have the policy automatically updated to reflect the new name and criteria via a database migration, with no manual intervention required.

## Why This Matters

The old, broadly-scoped policy generates alerts for resource settings (CPU limits, memory requests) that many teams intentionally leave unset, creating alert fatigue. Narrowing the scope to only the two most impactful settings (CPU request and memory limit) makes the policy more actionable and reduces false positives.
