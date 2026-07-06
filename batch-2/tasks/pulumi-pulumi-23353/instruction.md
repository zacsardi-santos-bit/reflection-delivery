I'm running into a dependency tracking issue in the Pulumi PCL interpreter when a component's input is wired to the output of another resource rather than a plain literal.

*   The Pulumi PCL program used by the l3-component-simple test must declare a top-level resource named 'input' of type 'simple:index:Resource' with a 'value' property set to true. The component named 'someComponent' must receive its 'input' argument from this resource's output value (input.value), not a literal boolean.

*   When a component's input is set to an output value from another resource, the component's resource registration must include all upstream resource URNs in its Dependencies list. The component must list the URN of every resource whose output contributes to its inputs.

*   When a component's input traces back to another resource's output, the component's resource registration must also populate PropertyDependencies, mapping each input property name to the list of resource URNs that property depends on. For example, if the 'input' property derives from a resource named 'input', then PropertyDependencies['input'] must contain that resource's URN.

*   Child resources inside a component that use a component input derived from an external resource's output must record the external resource's URN in their own Dependencies and PropertyDependencies. The dependency must propagate through the component boundary so the engine can correctly order all resource operations.

*   A top-level resource with a literal value must have empty Dependencies and empty PropertyDependencies in the snapshot, confirming it has no upstream resource dependencies.

*   The snapshot for the l3-component-simple test must contain exactly 5 resources: the stack, the provider (pulumi:providers:simple), the component, and two simple:index:Resource instances (one named 'input' at the top level and one named 'someComponent-res' as a child of the component).

*   The file at sdk/pcl/cmd/pulumi-language-pcl/testdata/projects/l3-component-simple/main.pp must be updated with the new program: add a top-level 'input' resource of type 'simple:index:Resource' with value=true, and change the component's input from the literal true to reference input.value.

*   The files at sdk/pcl/cmd/pulumi-language-pcl/testdata/eject-pcl/l3-component-simple/main.pp and sdk/pcl/cmd/pulumi-language-pcl/testdata/round-tripped-project/l3-component-simple/main.pp must also be updated with the same program changes as above.


*   Interface details: NO INTERFACES NEEDED

The required changes are to existing internal functions and test data files rather than to new public APIs. Specifically:

- The component registration logic in `pkg/pcl/runtime/interpreter.go` (function `registerComponent`) must be modified to extract dependency URNs from component inputs and include them in the resource registration request (both `Dependencies` and `PropertyDependencies` fields).
- The PCL test data files at the following paths must be updated to match the new program (add top-level `input` resource and wire component's input to `input.value`):
  - `sdk/pcl/cmd/pulumi-language-pcl/testdata/projects/l3-component-simple/main.pp`
  - `sdk/pcl/cmd/pulumi-language-pcl/testdata/eject-pcl/l3-component-simple/main.pp`
  - `sdk/pcl/cmd/pulumi-language-pcl/testdata/round-tripped-project/l3-component-simple/main.pp`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.