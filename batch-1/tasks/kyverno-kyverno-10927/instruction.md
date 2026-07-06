Implement a validation mechanism to ensure that policies with a missing or empty global context reference name are rejected at the API level. Adjust the serialization of the GlobalContextEntryReference struct to ensure the Name field is always present in the JSON representation.

*   Modify the GlobalContextEntryReference struct in `api/kyverno/v1/common_types.go`:
    *   Ensure the Name field is serialized as `json:"name"` without the `omitempty` option.
    *   The JSON representation must include the "name" key even if the Name field is empty.

*   Implement validation logic to enforce the presence of a global context reference name:
    *   When a ClusterPolicy is submitted, check the context entries for an empty or missing globalReference name.
    *   Reject the policy with a validation error if the name is not provided.
    *   The error message must specify that `spec.rules[0].context[0].globalReference.name` is a required value.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.