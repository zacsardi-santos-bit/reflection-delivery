## Description

The metadata code generator currently produces a single status file per component that bundles together two distinct concerns: component type/stability metadata and telemetry provider helper functions. This means every component's status file pulls in metrics and tracing dependencies even though those belong conceptually to a different layer. It also means the telemetry helpers were not using any component-specific identifier when acquiring meters and tracers from the provider — passing an empty string as the instrumentation scope — preventing observability tooling from properly attributing telemetry signals to the correct component package.

## Expected Behavior

- The generator should split output into two files: one for component type/stability information (containing only the component package import, the type variable, and stability-level constants), and a separate file for telemetry provider helpers (containing the metrics and tracing imports, plus the helper functions).
- The telemetry helper functions should acquire meters and tracers from the provider using the component's own module path as the instrumentation scope name, so that telemetry data is correctly attributed to the originating component.
- Both a new telemetry implementation template and a new telemetry test template should be embedded in the generator so they can be used during code generation.
- All existing components (receivers, exporters, processors, connectors, extensions) should have their generated files regenerated to reflect this new split structure.

## Why This Matters

Without proper instrumentation scope names, metrics and traces from different components can be indistinguishable in observability backends. Separating the generated files also keeps each file's dependencies minimal and improves clarity about what each generated artifact is responsible for.
