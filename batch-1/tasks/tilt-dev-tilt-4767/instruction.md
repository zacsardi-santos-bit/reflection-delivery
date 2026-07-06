Implement support for user-defined labels in Tilt by adding an optional parameter to resource configuration functions that allows users to specify labels as strings or lists of strings. Ensure these labels are stored in the resource's manifest for organizational purposes without triggering rebuilds.

*   Implement a new `LabelSet` type in `internal/tiltfile/value/label.go`.
    *   The `LabelSet` struct must have a `Values` field of type `map[string]string`.
    *   Implement the `Unpack(v starlark.Value) error` method for `LabelSet`.
        *   Accept a single string and produce a map where the string maps to itself.
        *   Accept a list or tuple of strings and produce a map where each string maps to itself.
        *   Reject invalid strings (e.g., containing special characters) with an error message containing "Invalid label" and "alphanumeric characters".
        *   Reject an empty string with an error message containing "name part must be non-empty".
        *   Reject unsupported types (e.g., dict/map) with an error message containing "value should be a label or List or Tuple of labels".

*   Update the `Manifest` struct in `pkg/model/manifest.go`.
    *   Add a `Labels` field of type `map[string]string`.
    *   Implement the `WithLabels(labels map[string]string) Manifest` method to return a copy of the manifest with the `Labels` field set to a deep copy of the provided map.
    *   Ensure differences in the `Labels` field do not cause build invalidation.

*   Modify the `k8s_resource` Tiltfile function.
    *   Accept an optional `labels` parameter (string or list of strings).
    *   Ensure the manifest's `Labels` field reflects a map where each label name is both key and value.
    *   Accumulate labels additively when called multiple times for the same resource.

*   Modify the `local_resource` Tiltfile function.
    *   Accept an optional `labels` parameter (string or list of strings).
    *   Ensure the manifest's `Labels` field reflects a map where each label name is both key and value.

*   Modify the `dc_resource` Tiltfile function.
    *   Accept an optional `labels` parameter (string or list of strings).
    *   Ensure the manifest's `Labels` field reflects a map where each label name is both key and value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.