Implement a public utility function in the shared provider utilities package to check if all specified label keys exist in a label map. Ensure the function handles edge cases such as empty maps or lists of keys gracefully.

*   Implement the `ContainsAllLabels` function in `internal/provider/utils/utils.go` with the following signature:
    *   `ContainsAllLabels(labels map[string]string, labelsToCheck []string) bool`
*   Ensure `ContainsAllLabels` returns:
    *   `true` when all keys in `labelsToCheck` are present in the `labels` map.
        *   Example: `labels={"label1": "foo", "label2": "bar"}`, `labelsToCheck=["label1", "label2"]` returns `true`.
    *   `false` when any key in `labelsToCheck` is not present in the `labels` map.
        *   Example: `labels={"label1": "foo", "label2": "bar"}`, `labelsToCheck=["label1", "label3"]` returns `false`.
    *   `false` when the `labels` map is empty and `labelsToCheck` is non-empty.
        *   Example: `labels={}`, `labelsToCheck=["label1", "label2"]` returns `false`.
    *   `true` when `labelsToCheck` is empty, regardless of the content of the `labels` map.
        *   Example: `labels={"label1": "foo", "label2": "bar"}`, `labelsToCheck=[]` returns `true`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.