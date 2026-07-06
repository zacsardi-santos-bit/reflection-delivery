## Description

When using automatic model selection, the tool has no awareness of the current phase of work (planning vs. implementation). This means it always uses the same model regardless of whether a user is actively constructing a plan or executing one. A better approach would be to automatically use a high-reasoning model during the planning phase — when architectural quality matters most — and then switch to a faster, more efficient model once a plan has been approved and the user moves to execution.

## Expected Behavior

- When the user is in plan mode (i.e., the approval mode is set to the planning variant), the tool should automatically route requests to the high-reasoning "Pro" model variant corresponding to the configured auto model family.
- After a plan has been approved and the user exits plan mode, the tool should detect the existence of the approved plan and automatically route requests to the faster "Flash" model variant.
- This behavior should only apply when an automatic model is configured — users who have explicitly selected a specific non-automatic model should be unaffected.
- The automatic routing behavior should be enabled by default but configurable so users can turn it off if desired.
- Telemetry events for routing decisions should include the current approval mode so operators can understand the context in which routing decisions were made.

## Why This Matters

Users working with the planning workflow currently get no performance optimization across phases. By routing intelligently based on plan status, the tool provides better plan quality without sacrificing speed during implementation. The configuration option ensures power users retain full control over model selection.
