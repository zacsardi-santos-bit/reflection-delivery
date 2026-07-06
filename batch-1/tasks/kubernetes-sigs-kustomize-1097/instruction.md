Implement a built-in namespace transformer plugin for Kustomize that correctly applies a target namespace across a set of Kubernetes resources while adhering to specific transformation rules. Ensure that cluster-scoped resources remain unchanged and handle role bindings with or without subjects gracefully.

*   Create a Go plugin file at `plugin/builtin/namespacetransformer/NamespaceTransformer.go` in `package main`.
    *   Export a variable named `KustomizePlugin` of the `plugin` struct type.
    *   Implement `Config` and `Transform` methods in the `plugin` struct.

*   Implement the `plugin` struct with the following fields:
    *   `Namespace` (string) - JSON/YAML tag: `namespace,omitempty`.
    *   `FieldSpecs` ([]config.FieldSpec) - JSON/YAML tag: `fieldSpecs,omitempty`.

*   Implement the `Config` method with the following requirements:
    *   Reset `Namespace` to an empty string and `FieldSpecs` to nil before unmarshalling.
    *   Unmarshal the configuration bytes into the `plugin` struct using `yaml.Unmarshal`.

*   Implement the `Transform` method with the following requirements:
    *   Set the namespace on all namespace-scoped resources to the configured target namespace, adding or overwriting as necessary.
    *   Delegate transformation to `transformers.NewNamespaceTransformer(p.Namespace, p.FieldSpecs).Transform(m)`.

*   Ensure cluster-scoped resources are not modified:
    *   Skip modification of metadata namespace for the following kinds: `Namespace`, `CustomResourceDefinition`, `ClusterRole`, `ClusterRoleBinding`, `PersistentVolume`.

*   Handle `ClusterRoleBinding` resources with subjects:
    *   Update the namespace of subjects whose kind is `ServiceAccount` and match a `ServiceAccount` resource in the transformed set.
    *   Leave unchanged the namespace of subjects that do not match any transformed `ServiceAccount`.

*   Fix the bug in `pkg/transformers/namespace.go`:
    *   In `updateClusterRoleBinding`, guard the type assertion on the "subjects" field:
        *   Use `subjects, ok := objMap["subjects"].([]interface{})`.
        *   Skip the resource if `subjects` is nil or `ok` is false to prevent a panic.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.