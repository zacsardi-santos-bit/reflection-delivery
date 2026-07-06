## Description

The tracing subsystem already supports selectively enabling or disabling individual tracers based on their instrumentation scope, including glob-pattern matching. The metrics subsystem has no equivalent feature — there is currently no way to disable measurement collection for specific meters without shutting down the entire metrics pipeline.

## Expected Behavior

- Users should be able to provide a custom configurator function when creating a meter provider that controls whether individual meters are enabled or disabled based on their instrumentation scope.
- The configurator should be replaceable at runtime, immediately affecting all existing meters as well as any new ones created afterward.
- A built-in "disable all" configurator should be available for convenience.
- A rule-based configurator should be available that evaluates a list of predicate/config pairs in order (first match wins) and falls back to a default when no rule matches.
- A glob-pattern predicate utility, currently only available in the tracing module, should be consolidated into a shared location so both tracing and metrics can use it.
- When a configurator function raises an exception, the system should fall back gracefully to the default (enabled) behavior and log the error rather than crashing.
- Custom meter configurators should be loadable via an environment variable backed by an entry-point mechanism, consistent with how the tracer configurator works.

## Why This Matters

Instrumentation libraries often emit metrics for components a user may not care about. Without per-meter enable/disable control, the only option is to filter or drop metrics at export time, which wastes CPU and memory on unnecessary collection. This feature allows fine-grained, scope-based control over which meters are active.
