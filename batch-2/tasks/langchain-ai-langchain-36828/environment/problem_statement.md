## Description

When LangChain normalizes runnable configuration objects, it already promotes the model name from the configurable settings into metadata so that tracing and observability tools can see it. However, the checkpoint namespace — which identifies which sub-graph or nested execution scope a run belongs to — is not similarly promoted. This means monitoring systems relying on config metadata are unaware of the active checkpoint namespace even when it is specified in the configurable settings.

## Expected Behavior

- When a config's configurable settings contain a checkpoint namespace string value, that value should be automatically surfaced in the config's metadata dictionary (as long as it isn't already set there).
- When a checkpoint namespace is provided as a top-level config key, it should be handled the same way as other top-level keys — moved into the configurable settings — and also copied into metadata.
- The existing behavior for the model name field should continue to work as before, and configs containing both a model name and a checkpoint namespace in their configurable settings should have both values appear in metadata.

## Why This Matters

Without this propagation, tooling that inspects config metadata for run context (such as which checkpoint namespace a run belongs to) misses this information even when it is explicitly configured. Making checkpoint namespace metadata-visible keeps it consistent with how the model name is already handled.
