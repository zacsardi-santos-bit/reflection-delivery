## Description

The current load boost module is tightly coupled to cluster-level autoscaling: every function that reads or writes a boost requires the caller to supply a region and a pool, with the internal Zookeeper path derived from those values. This design prevents the same boost mechanism from being reused for other autoscaling contexts (e.g. service-level autoscaling), because the abstraction assumes cluster identity rather than accepting a generic storage path.

Additionally, the cluster boost command-line tool conflates argument parsing and business logic inside a single function. This makes the core behavior difficult to invoke programmatically or to test without going through the CLI layer.

## Expected Behavior

- The boost module should be renamed to reflect its general-purpose nature (applicable to more than just cluster autoscaling).
- Core functions that read or write boost state should accept a pre-computed Zookeeper path directly, rather than a region and pool pair that they derive into a path themselves.
- A dedicated helper function should remain available to construct the cluster-specific Zookeeper path from a region and pool.
- The cluster boost CLI tool's business logic function should accept explicit named parameters and return a boolean, with all argument parsing and process exit handling moved into a separate entry point function.
- When the set action is used in the cluster context, external metrics reporting should be explicitly opted into via a parameter, so that non-cluster callers don't trigger cluster-specific side effects.

## Why This Matters

Separating path construction from boost state management allows the boost mechanism to be reused across different autoscaling targets without duplication. Decoupling argument parsing from business logic in the CLI tool makes the core function independently callable, unit-testable, and composable with other tools.
