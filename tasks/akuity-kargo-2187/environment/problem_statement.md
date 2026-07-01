## Description

Currently, a Stage's status tracks its currently deployed freight as a single flat reference. This design only works for Stages that receive freight from one warehouse, but breaks down when a Stage is expected to aggregate freight from multiple warehouses simultaneously. There is no way to tell which freight came from which warehouse, and verification/analysis operations lack the context they need to work correctly in multi-warehouse scenarios.

## Expected Behavior

- The Stage status should maintain a structured history of deployed freight, where each history entry is a collection of freight items keyed by their originating warehouse name.
- The most recent entry in this history should represent what is currently deployed across all contributing warehouses.
- Verification and analysis run operations should accept the specific freight item they are operating on, rather than always reading from a single global "current freight" pointer on the stage status.
- When a Stage is a control-flow-only stage (not responsible for deployments), all freight tracking fields should be cleared from its status.
- Indexing of stages by analysis run should scan across all freight items in the history, not just the single current freight reference.
- The older single-reference fields should be retained for backward compatibility but marked as deprecated in favor of the new history structure.

## Why This Matters

As Kargo grows to support more complex pipelines where a single Stage might consume freight from several independent warehouses, the system needs to track and verify each piece of freight independently. Without this, operators cannot see which warehouse contributed which freight, and automated verification steps may operate on the wrong freight context. This change lays the foundation for proper multi-warehouse freight tracking.
