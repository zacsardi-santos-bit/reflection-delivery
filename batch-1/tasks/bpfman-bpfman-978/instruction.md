Update the eBPF agent controller package to resolve a build failure caused by a function name inconsistency. Implement the function with the updated name to ensure the package compiles and all tests pass.

*   Implement the `getPodsForNode` function in the `bpfman-operator/controllers/bpfman-agent` package.
    *   Use the package name `bpfmanagent`.
    *   Define the function signature as: `getPodsForNode(ctx context.Context, clientset kubernetes.Interface, containerSelector *bpfmaniov1alpha1.ContainerSelector, nodeName string) (*v1.PodList, error)`.
*   Ensure `getPodsForNode` performs the following:
    *   Accepts a `context.Context`, a `kubernetes.Interface` clientset, a `*bpfmaniov1alpha1.ContainerSelector`, and a `nodeName` string.
    *   Returns a `*v1.PodList` and an `error`.
    *   Filters pods by node using the Kubernetes field selector `spec.nodeName=<nodeName>`.
    *   Queries pods within the namespace specified by `containerSelector.Namespace`.
    *   Applies the ContainerSelector's Pods label selector as an additional filter if it is non-empty and not `"<none>"`.
*   Verify that the entire `bpfman-operator/controllers/bpfman-agent` package compiles successfully.
*   Confirm that all agent controller tests (kprobe, tc, xdp, tracepoint, uprobe, discovered program) run without errors once the function is correctly implemented.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.