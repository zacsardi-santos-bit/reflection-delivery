Implement the necessary changes in the Dashboard-as-Code SDKs to ensure that the filter auto-generation utility uses regex match operators for multi-select variables. Update the Go SDK's variable builders and the CUE SDK's filter utility to accommodate this change.

*   Update the Go SDK:
    *   Modify the `ApplyFilters()` method in `go-sdk/prometheus/variable/label-names/label-names.go` to use the regex match operator (`=~`) instead of the exact equality operator (`=`).
        *   Ensure the format is `variableName=~"$variableName"` for each filter variable.
    *   Modify the `ApplyFilters()` method in `go-sdk/prometheus/variable/label-values/label-values.go` to use the regex match operator (`=~`) instead of the exact equality operator (`=`).
        *   Ensure the format is `variableName=~"$variableName"` for each filter variable.

*   Update the CUE SDK:
    *   In `cue/dac-utils/prometheus/filter/filter.cue`, ensure the filter utility generates label matchers using the regex match operator (`=~`) for both `TextVariable` and `ListVariable` kinds.
        *   Exclude the `LabelNames` plugin kind.
        *   Change the template from `\(var.#name)="$\(var.#name)"` to `\(var.#name)=~"$\(var.#name)"`.

*   Ensure that example panel definitions demonstrate how to build reusable, configurable panel templates.
    *   Include an optional aggregation grouping clause that can be set when instantiating the panel for different contexts.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.