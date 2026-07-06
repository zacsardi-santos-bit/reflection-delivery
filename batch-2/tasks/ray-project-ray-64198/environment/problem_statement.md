## Description

Ray Data's telemetry system records workload plans and execution results for usage analytics, but it currently has two gaps:

1. **No operator identifiers in the workload plan.** Operators in the recorded plan tree and flat operator list carry only their anonymized names. When the same operator type appears more than once (or when an operator is referenced from multiple branches of a shared-plan DAG), there is no way to distinguish them. Every node needs a stable short identifier that can be used to correlate references across different parts of the payload.

2. **Detected issues are never included in the usage payload.** When the issue detectors identify problems (such as a hanging operator or excessive memory usage) during pipeline execution, that information is silently discarded — it never makes it into the telemetry record. Downstream analytics therefore have no visibility into how often or in which operators issues occur across user workloads.

## Expected Behavior

- Every operator node in the workload plan, and every entry in the flat operator list, should carry a stable unique identifier so that references to the same operator from different parts of the payload can be correlated.
- When the same operator instance is shared across multiple plan branches, it should receive exactly one identifier (no duplicates).
- After execution finishes, any issues detected during that run should be included in the usage payload as a list of records describing the type of issue and which operator it affected.
- If no issues were detected, the corresponding field in the payload should be an empty list rather than absent.
- The component responsible for tracking detected issues must deduplicate them — reporting the same issue for the same operator twice should not create duplicate records.
- Physical operators that were fused from multiple logical stages should expose their full composition in a way that preserves traceability back to individual logical operators and their identifiers.

## Why This Matters

Without operator identifiers, telemetry cannot distinguish operators of the same type, and issue reports cannot be matched to specific nodes in the workload plan. Without issue data in the payload, the team has no aggregate view of how often execution problems occur or which operators are most affected. Both gaps reduce the actionability of Ray Data's telemetry.
