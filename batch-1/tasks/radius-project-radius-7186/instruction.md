Implement automatic port-forwarding for the Radius Dashboard alongside the application when running the CLI command. Ensure the tool detects the dashboard's presence in the cluster and sets up port-forwarding using Kubernetes label selectors.

*   Implement the `CreateLabelSelectorForApplication` function in `pkg/cli/kubernetes/portforward/labels.go`:
    *   Accept an application name string.
    *   Return a Kubernetes label selector with the string "radapp.io/application=<appName>".
    *   Ensure it returns a non-nil selector and nil error for valid input.

*   Implement the `CreateLabelSelectorForDashboard` function in `pkg/cli/kubernetes/portforward/labels.go`:
    *   Return a Kubernetes label selector with the string "app.kubernetes.io/name=dashboard,app.kubernetes.io/part-of=radius".
    *   Ensure the keys are in the specified order and return a non-nil selector and nil error.

*   Update the `Options` struct in `pkg/cli/kubernetes/portforward/`:
    *   Replace the `ApplicationName` string field with a `LabelSelector` field of type `labels.Selector`.

*   Modify the `findStaleReplicaSets` function in `pkg/cli/kubernetes/portforward/util.go`:
    *   Change the signature to remove the `appName` string parameter.
    *   Add a `labelSelector labels.Selector` as the last parameter.

*   Update the `Runner` struct in `pkg/cli/cmd/run/run.go`:
    *   Include an unexported `kubernetesClient` field of type `k8sclient.Interface`.

*   Enhance the `Runner.Run()` method:
    *   Detect a dashboard deployment in the "radius-system" namespace using `CreateLabelSelectorForDashboard`.
    *   Start a port-forward session for the dashboard if detected.
    *   Use the "radius-system" namespace, the kube context from the workspace, and the label selector from `CreateLabelSelectorForDashboard`.

*   Ensure application port-forwarding:
    *   Use the application's compute namespace and the kube context from the workspace.
    *   Use the label selector from `CreateLabelSelectorForApplication(appName)`.

*   Handle scenarios where no dashboard deployment is detected:
    *   Start only the application port-forward session.

*   Detect dashboard deployment by querying Kubernetes for deployments in the "radius-system" namespace matching the label selector "app.kubernetes.io/name=dashboard,app.kubernetes.io/part-of=radius".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.