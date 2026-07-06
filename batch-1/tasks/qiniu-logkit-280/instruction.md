Implement a new transformer in the Logkit mutate package that adds a static annotation to each log record. The transformer should handle field conflicts according to specified rules and report its error statistics.

*   Define a struct named `Label` in the `transforms/mutate` package, located in `transforms/mutate/label.go`.
    *   Exported fields must be: `Key` (string), `Value` (string), and `Override` (bool).

*   Implement the `Transform` method for the `Label` struct:
    *   Accept a slice of `sender.Data` records as input.
    *   Return the same slice with mutations applied and an error value.
    *   For each record:
        *   If the field named by `Key` does not exist, add the `Key=Value` pair to the record.
        *   If the field named by `Key` exists and `Override` is false, leave the record unchanged, increment `Stats().Errors` by 1, and return a non-nil error.
        *   If the field named by `Key` exists and `Override` is true, replace the existing value with `Value` and return nil.

*   Implement the `Stage` method for the `Label` struct:
    *   Return the string constant `transforms.StageAfterParser`.

*   Implement the `Stats` method for the `Label` struct:
    *   Return a value whose `Errors` field (type `int64`) accumulates the total count of records skipped due to key conflicts across all invocations of `Transform`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.