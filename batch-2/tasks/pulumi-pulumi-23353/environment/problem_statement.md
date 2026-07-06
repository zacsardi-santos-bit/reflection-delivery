## Description

When a component resource receives an output value from another resource as one of its inputs — rather than a plain literal — the engine fails to record any dependency between the component and the resource that provided that output. As a result, the engine has no way to know that the component must be created after the upstream resource, which can lead to operations being performed in the wrong order.

## Expected Behavior

- When a component's input is wired to another resource's output, the component's registration should reflect a dependency on that upstream resource — both in its overall dependency list and per-input-property dependency tracking.
- When resources inside a component use the component's input, and that input ultimately traces back to an external resource's output, those internal resources should also record the external resource as a dependency. The dependency chain must not break at the component boundary.
- A resource whose inputs are all literal values should have no recorded dependencies.

## Why This Matters

Without proper dependency propagation across component boundaries, the infrastructure engine cannot safely determine the correct order in which to create, update, or delete resources. This can cause race conditions or failures when a resource depends — even indirectly through a component — on the output of another resource that has not yet been created.
