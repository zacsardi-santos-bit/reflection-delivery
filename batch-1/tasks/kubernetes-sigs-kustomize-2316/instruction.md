Implement a new filter component for the Kustomize project to update replica counts in Kubernetes resources. The filter should modify resources based on a specified target name and update specified field paths with a new replica count, respecting a creation flag for non-existent fields.

*   Create a `Filter` struct in `api/filters/replicacount/replicacount.go` with the following fields:
    *   `Replica` of type `types.Replica` to store the target resource name and desired replica count.
    *   `FsSlice` of type `types.FsSlice` to store the list of field paths where replicas are configured.

*   Ensure the `Filter` struct implements the `kio.Filter` interface with the method signature:
    *   `Filter(nodes []*yaml.RNode) ([]*yaml.RNode, error)`

*   Implement the following behavior in the `Filter` method:
    *   Modify only those resources whose `metadata.name` matches `Replica.Name`.
    *   For matching resources, update each field path in `FsSlice` to `Replica.Count` as a string.
    *   If a field path exists, update its value to the new count.
    *   If a field path does not exist:
        *   Create the field with the new count if `CreateIfNotPresent` is true for that path.
        *   Leave the resource unchanged if `CreateIfNotPresent` is false or unset.
    *   Support updating multiple field paths in a single pass.

*   Ensure compatibility with the Kustomize filter architecture, allowing the filter to process any resource type by matching only on `metadata.name`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.