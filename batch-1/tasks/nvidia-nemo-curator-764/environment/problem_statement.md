## Description

When building data curation pipelines, it's common to want to reuse an existing processing stage with minor adjustments — different resource allocations, a different display name, or a different batch size — without having to subclass it or modify the original stage definition. Currently, there is no clean way to do this; each variation requires either a new subclass or direct mutation of the original stage object.

Similarly, for composite stages (stages composed of multiple sub-stages), there is no mechanism to configure individual sub-stages by name before the pipeline is executed.

## Expected Behavior

- A method should be available on processing stages that creates a modified copy with specified property overrides (name, resources, batch size), while leaving the original stage completely unchanged.
- Passing no override (or explicitly passing nothing) for a property should preserve the original value.
- The method should return the new instance in a way that supports chaining.
- For composite stages, a corresponding method should allow accumulating named-stage configurations that are applied when the composite stage is expanded. This method should return the same composite instance to support fluent-style usage.
- Applying accumulated configurations to a list of sub-stages should work sequentially: configurations applied in a later call can reference names introduced by earlier calls.
- If stage names within a composite are not unique, or a referenced stage name does not exist, a descriptive error should be raised.
- The composite stage's declared inputs should come from its first sub-stage, and its declared outputs should come from its last sub-stage.

## Why This Matters

This feature makes pipelines more composable and reusable. Instead of defining new stage classes for every minor configuration variant, developers can derive customized stage instances inline when building the pipeline, improving readability and reducing boilerplate.
