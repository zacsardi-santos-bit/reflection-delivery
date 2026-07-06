## Description

There is currently no way to configure default compute resource requests and limits for inference service containers through the system's configuration object. The default CPU and memory values that get applied to serving components are set through a globally shared variable, which means there is no proper operator-facing mechanism to customize these defaults, and the global state can cause unpredictable behavior when different parts of the system run concurrently or when tests run in sequence.

Operators need to be able to define default resource requests and limits (CPU and memory) as part of the standard inference service configuration so that all serving runtimes consistently pick up those values when no explicit resources are specified by the user.

## Expected Behavior

- The inference service configuration object should include a dedicated section for specifying default resource limits and requests (CPU and memory).
- All serving runtime types (including all built-in predictors, explainers, and transformers) should read their default resource requirements from the provided configuration object rather than from a globally mutable value.
- When a configuration specifies particular CPU and memory values, containers that have no explicit resource requirements should have those config-driven values applied as both requests and limits.
- The Python SDK should expose a corresponding model type for this new resource configuration section, and the inference service configuration model should accept it.

## Why This Matters

Without a configuration-driven approach, operators cannot reliably customize default resource assignments for inference workloads. This gap also makes the system difficult to test reliably and prevents different deployments from having different resource defaults without code changes.
