Implement the following changes to the uprobe program controller in the bpfman operator to simplify the API and enhance functionality for container-aware uprobe attachment.

* Update the UprobeProgram custom resource:
    * Change the `Targets` field from a list of strings to a single `Target` field of type string in `bpfman-operator/apis/v1alpha1/uprobeProgram_types.go`.
    * Ensure the `Target` field uses the json tag `json:"target"`.

* Modify the gRPC attachment process:
    * Ensure the `ContainerPid` field is omitted from the uprobe attachment request when no container selector is configured.

* Implement pod discovery functionality:
    * Create a `getPods` function in `bpfman-operator/controllers/bpfman-agent/common.go` with the signature:
      `getPods(ctx context.Context, clientset kubernetes.Interface, containerSelector *bpfmaniov1alpha1.ContainerSelector, nodeName string) (*v1.PodList, error)`.
    * Filter pods by the specified node using the `spec.nodeName` field selector.
    * Apply the label selector from `ContainerSelector.Pods` to filter pods; return all pods on the node if the selector is empty.
    * Scope pod listing to the namespace specified in `ContainerSelector.Namespace`; include all namespaces if this field is empty.

* Define the `ContainerSelector` struct:
    * Add the `ContainerSelector` type in `bpfman-operator/apis/v1alpha1/shared_types.go`.
    * Include a `Namespace` field of type string and a `Pods` field of type `metav1.LabelSelector`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.