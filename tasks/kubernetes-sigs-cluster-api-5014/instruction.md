Implement the `fixTargetNamespace` function to update namespace references in Kubernetes provider manifests. Ensure that all relevant objects have their namespace fields updated to the specified target namespace, and handle errors appropriately.

*   Implement the `fixTargetNamespace` function with the following signature:
    *   `fixTargetNamespace(objs []unstructured.Unstructured, targetNamespace string) ([]unstructured.Unstructured, error)`
    *   Location: `cmd/clusterctl/client/repository/components.go`
*   Update namespace references for different types of objects:
    *   For `Namespace` kind objects (`apiVersion v1`):
        *   Update the object's `metadata.name` to the `targetNamespace` value.
    *   For namespaced objects (e.g., Deployments, Services):
        *   Update the `metadata.namespace` field to the `targetNamespace` value.
    *   For cluster-scoped objects (e.g., `ClusterRole`):
        *   Leave them unchanged as they do not have a namespace in their metadata.
    *   For `MutatingWebhookConfiguration` and `ValidatingWebhookConfiguration` objects (`apiVersion admissionregistration.k8s.io/v1`):
        *   Update the `namespace` field inside each webhook's `clientConfig.service` to the `targetNamespace` value.
    *   For `CustomResourceDefinition` objects (`apiVersion apiextensions.k8s.io/v1`) with a webhook conversion strategy:
        *   Update the `namespace` field inside `spec.conversion.webhook.clientConfig.service` to the `targetNamespace` value.
    *   For objects with a `cert-manager.io/inject-ca-from` annotation:
        *   Replace the namespace portion of the annotation value (format: `namespace/cert-name`) with the `targetNamespace`, resulting in `targetNamespace/cert-name`.
*   Ensure `fixTargetNamespace` returns a nil error for all valid inputs and propagates errors when processing fails.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.