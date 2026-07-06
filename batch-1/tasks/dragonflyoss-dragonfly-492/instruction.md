Implement two utility functions in the `tasks` package to handle serialization and deserialization of Go values for a distributed task queue system. Ensure these functions provide a standard mechanism for converting data structures into the required wire format and back, maintaining data integrity.

*   Implement `MarshalTaskArg` in `internal/tasks/tasks.go`:
    *   Accept any Go value as `taskArg`.
    *   Return a slice of `machineryv1tasks.Arg` containing exactly one element.
    *   Set the `Value` field of the returned element to the JSON-encoded string of `taskArg`.
    *   Use the import alias `machineryv1tasks` for "github.com/RichardKnop/machinery/v1/tasks".
    *   Ensure no error occurs when given a valid serializable value.

*   Implement `UnmarshalTaskResult` in `internal/tasks/tasks.go`:
    *   Accept a slice of `reflect.Value` as `taskResult` and a destination pointer `v`.
    *   Decode the first element of `taskResult` as a JSON string into `v`.
    *   Return `nil` on successful deserialization.
    *   Return an error if the slice contains more than one element or if deserialization fails.

*   Ensure round-trip fidelity:
    *   A value serialized with `MarshalTaskArg` and then deserialized with `UnmarshalTaskResult` must be equal to the original input value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.