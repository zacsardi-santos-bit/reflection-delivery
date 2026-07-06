Implement a priority-based file writing system for a secrets management tool. Add support for specifying file write order using a numeric priority and restructure the configuration format to decouple file lists from individual secrets. Ensure the system maintains order through state persistence and handles legacy state files without explicit priorities.

*   Define a `PriorityFile` struct in `state.go`:
    *   Fields: `Path` (string), `Priority` (int).
    *   Serialize `Path` as a plain JSON string; do not serialize `Priority`.

*   Define a `PriorityFileSortedList` type in `state.go`:
    *   Type: `[]PriorityFile`.
    *   Implement `sort.Interface` with `Len`, `Swap`, and `Less` methods.
    *   Sort by `Priority` ascending; use `Path` as a lexicographic tiebreaker.

*   Update `SecretState`:
    *   Change `FilesUsing` field to `PriorityFileSortedList`.
    *   Signature: `FilesUsing PriorityFileSortedList \`json:"files_using,omitempty"\``

*   Implement `RegisterUsage` method in `state.go`:
    *   Signature: `func (s *SecretState) RegisterUsage(path string, priority int)`.
    *   Register a file path with a given priority.
    *   Prevent duplicate entries; append new entries and re-sort the list.

*   Support JSON serialization/deserialization:
    *   Serialize `PouchState` including `SecretState.FilesUsing` as a `PriorityFileSortedList`.
    *   Deserialize `PriorityFileSortedList` from a plain string array, assigning priorities as `index * 10`.

*   Update configuration handling:
    *   Support a top-level `files` key in `pouchfile` YAML.
    *   Allow an optional `priority` integer field on each file entry.
    *   Preserve and use `priority` when registering file usage.

*   Ensure state persistence:
    *   Persist state using `state.Save()` and allow reloading without data loss.
    *   When loading legacy state files, assign priorities based on entry order.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.