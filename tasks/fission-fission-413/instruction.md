Implement the ability for users to customize runtime and builder containers in the Fission serverless framework by merging user-specified container configurations with system-defined specifications. Create a utility function to merge multiple Kubernetes container specifications, prioritizing system-defined values while allowing user-defined configurations for unset fields.

Requirements:
*   Implement the `MergeContainerSpecs` function in the root `fission` package.
    *   Accept a variadic number of `*apiv1.Container` pointers.
    *   Return a merged `apiv1.Container` value (not a pointer).
*   Ensure the function behaves as follows:
    *   Return an empty container value when called with no arguments.
    *   Return a container value equal to the input container when called with a single container pointer.
    *   Use a first-non-zero-value-wins strategy for scalar fields (e.g., Name, Image, Command, Args, ImagePullPolicy, TTY) when merging multiple container pointers.
    *   Accumulate list-typed fields (e.g., environment variables `Env`) from all specs in order.
    *   Maintain order sensitivity: the order of input specs affects the result when there are conflicting non-zero scalar values.
*   Define the function in any `.go` file in the root `fission` package (e.g., `common.go` or `merge.go`).
*   Use the import alias `apiv1 "k8s.io/client-go/pkg/api/v1"` for the Kubernetes container type.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.