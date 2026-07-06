Implement a feature to allow operators to specify a maximum pod count per node in the host configuration for a Kubernetes cluster management tool. Ensure that this new configuration field is optional and validate its value to prevent invalid configurations.

*   Update the `KubeletConfig` struct in `pkg/apis/kubeone/types.go`:
    *   Add a new field `MaxPods` of type `*int32` (optional pointer to a 32-bit integer).
    *   This field allows operators to specify the maximum number of pods per node.
    *   If `MaxPods` is set, it must be a positive integer; if nil, the runtime default is used.

*   Modify the `ValidateHostConfig` function in `pkg/apis/kubeone/validation/validation.go`:
    *   Extend the validation logic to include the `MaxPods` field.
    *   If `MaxPods` is set (non-nil) and its value is less than or equal to 0, return a validation error.
    *   Use `field.Invalid` to append an error for the path `kubelet.maxPods` if the value is invalid.
    *   If `MaxPods` is set to a positive integer, ensure no error is returned for this field.
    *   If `MaxPods` is nil, do not return an error, allowing the system to use the kubelet default.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.