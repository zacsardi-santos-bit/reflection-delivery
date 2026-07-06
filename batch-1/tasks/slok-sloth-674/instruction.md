Implement a new contrib plugin for the Sloth SLO framework to correct SLI error ratios based on traffic volume. Ensure the plugin only supports services using events-based SLI types and validates SLI query templates. Generate corrected SLI error recording rules and metadata rules for valid SLOs.

*   Implement the plugin as a Go package:
    *   Name the package 'plugin' and place it in `internal/plugin/slo/contrib/denominator_corrected_rules_v1/plugin.go`.
    *   Export constants `PluginVersion = "prometheus/slo/v1"` and `PluginID = "sloth.dev/contrib/denominator_corrected_rules/v1"`.

*   Create the `NewPlugin` function:
    *   Accept a JSON configuration object (can be empty) and return a value implementing the `pluginslov1.Plugin` interface or an error.
    *   Signature: `NewPlugin(c json.RawMessage, appUtils pluginslov1.AppUtils) (pluginslov1.Plugin, error)`.

*   Implement the `ProcessSLO` method:
    *   Signature: `ProcessSLO(ctx context.Context, request *pluginslov1.Request, result *pluginslov1.Result) error`.
    *   Return an error if the SLO's SLI does not use the events type (e.g., Events is nil or empty).
    *   Validate SLI error query templates:
        *   Return an error for syntactically invalid Go template expressions.
        *   Return an error if the template references undefined variables; only 'window' is supported.
    *   On success, populate `result.SLORules.SLIErrorRecRules.Rules` with corrected SLI error recording rules:
        *   Create one rule per unique time window from the MWMB alert group and the SLO's total window.
        *   Use the format: `Record = "slo:sli_error:ratio_rate{window}"` and `Expr` in the specified multiline format.
        *   Include labels merging SLO ID labels, the `sloth_window` label, and SLO custom labels.
    *   Populate `result.SLORules.MetadataRecRules.Rules` with numerator correction rules:
        *   Use the format: `Record = "slo:numerator_correction:ratio{window}"` and `Expr` in the specified single-line format.
        *   Include labels merging SLO ID labels and SLO custom labels, excluding the `sloth_window` label.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.