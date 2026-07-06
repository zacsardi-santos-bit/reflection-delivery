Implement a mechanism to allow users to opt a specific ingress out of Application Load Balancer (ALB) sharing by adding an annotation. Ensure the internal representation of the ingress reflects this sharing preference accurately.

*   Update the Ingress struct in `kubernetes/adapter.go`:
    *   Add a boolean field named `shared` to indicate whether the ingress can share an ALB with other ingresses.
    *   Default the `shared` field to `true`, indicating sharing is allowed.

*   Define a new annotation constant in `kubernetes/ingress.go`:
    *   Name the constant `ingressSharedAnnotation`.
    *   Set its value to the string `"zalando.org/aws-load-balancer-shared"`.

*   Modify the `newIngressFromKube` function in `kubernetes/adapter.go`:
    *   Signature: `newIngressFromKube(kubeIngress *ingress) *Ingress`
    *   Read the `ingressSharedAnnotation` from the Kubernetes ingress metadata annotations.
    *   Default the `shared` field to `true` when the annotation is absent or has any value other than `"false"`.
    *   Set the `shared` field to `false` only when the annotation value is exactly `"false"`.

*   Modify the `newIngressForKube` function in `kubernetes/adapter.go`:
    *   Signature: `newIngressForKube(i *Ingress) *ingress`
    *   Include `ingressSharedAnnotation` in the returned ingress metadata annotations.
    *   Set the annotation value to `"true"` when the `shared` field is `true`.
    *   Set the annotation value to `"false"` when the `shared` field is `false`.

*   Ensure a complete roundtrip conversion:
    *   Convert from a Kubernetes ingress to the Ingress struct and back.
    *   Maintain structural equality of the Kubernetes ingress object.
    *   Preserve the `ingressSharedAnnotation` annotation with the correct string value for both `shared=true` and `shared=false` cases.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.