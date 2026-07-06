## Description

Node Feature Discovery currently has no unified mechanism for enabling or disabling specific experimental or beta-stage features at runtime. Individual features are toggled through ad-hoc boolean flags scattered across the codebase, making it hard to manage feature lifecycle (alpha → beta → stable → deprecated) consistently. We need a proper feature-gating system that lets operators and developers control which capabilities are active via a single, consistent command-line flag.

## Expected Behavior

- A new utility package provides a feature gate abstraction. Each gate has a name, a default enabled/disabled state, a maturity label (alpha, beta, stable, or deprecated), and an optional lock that permanently fixes the gate to its default value.
- A command-line flag accepts a comma-separated list of name=value pairs to override feature defaults at startup.
- When listing known features (e.g. for help text), only alpha and beta features are shown — stable and deprecated features are hidden.
- Attempting to set an unknown feature gate returns a descriptive error. Attempting to set a locked gate to a non-default value returns an error with the gate name, attempted value, and locked value.
- The string representation of a feature gate lists only explicitly-set features in alphabetical order.
- A deep copy operation produces an independent copy that preserves any overridden defaults.
- An NFD-specific features package exposes a shared mutable feature gate, a map of default feature definitions, and a constant for the Node Feature API gate (a beta feature enabled by default). Test setup in both the master and worker packages must initialize this shared gate and disable the Node Feature API gate before creating test instances.

## Why This Matters

Without a structured feature gate system, there is no safe or consistent way to introduce, graduate, or deprecate individual capabilities. Operators cannot selectively enable or disable experimental behavior for their environments, and the codebase accumulates one-off flags that have no common contract around lifecycle, discoverability, or locking.
