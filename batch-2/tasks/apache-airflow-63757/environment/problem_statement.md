## Description

Airflow tracks many operational metrics (scheduler heartbeats, pool slots, task durations, etc.) through a central metrics registry that documents every metric the project supports. Currently, there is no automated check to verify that every metric call made in the codebase actually corresponds to a registered metric. This means a developer can add a new metric call and forget to register it, or rename a call without updating the registry, and no CI check will catch the discrepancy.

## Expected Behavior

A CI pre-check tool should be available that can:

- Scan Python source files to find all places where metrics are tracked, regardless of whether the metric name is a plain string, a dynamically constructed string, or a formatted string with embedded variables.
- Normalize metric names for comparison, handling both the current metric naming convention and any legacy naming formats documented in the registry.
- Look up each discovered metric against the central metrics registry, matching by exact name, by structural equivalence when variable placeholders differ, by legacy name, or by recognizing that a dynamic metric is a valid expansion of a registered prefix.
- Report which metric calls were found, including the method used, the object on which the call was made, whether the metric name is dynamic, the line number, and the source file.

## Why This Matters

Without this tooling, the metrics registry can silently drift out of sync with what the codebase actually records. Developers cannot easily audit whether all metrics in use are documented, and newly introduced metrics may go unregistered. Automating this check in CI prevents such drift and ensures the registry remains the authoritative source of truth for all metrics the project emits.
