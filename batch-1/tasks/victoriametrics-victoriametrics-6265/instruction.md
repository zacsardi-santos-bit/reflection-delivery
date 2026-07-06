Implement enhancements to the statsd protocol parser to preserve and utilize the metric type field, enforce its presence, and correctly handle multiple values. Update the parsing logic to ensure that all requirements are met.

*   Modify the `Row` struct in `lib/protoparser/statsd/parser.go`:
    *   Replace the `Value float64` field with `Values []float64` to store multiple numeric values from a single statsd line.

*   Define a constant for the metric type tag:
    *   Add `const statsdTypeTagName = "__statsd_metric_type__"` in `lib/protoparser/statsd/parser.go`.

*   Update the parsing logic in `Rows.Unmarshal` method in `lib/protoparser/statsd/parser.go`:
    *   Require the presence of a metric type field using the '|' separator. Lines without this separator should return a parse error.
    *   Parse multiple colon-separated numeric values (DogStatsD v1.1 extension) and store them in `Row.Values`. Ensure all segments are valid floats; otherwise, return a parse error.
    *   Add the metric type as the first entry in `Row.Tags` with `Key` set to `statsdTypeTagName` and `Value` set to the type string.
    *   Ensure the type tag appears before any user-defined tags in `Row.Tags`. Drop tags with empty keys or values, except for the type tag.
    *   Silently ignore extended protocol suffixes like container IDs and Unix timestamps after the user tags.

*   Ensure the stream parser produces rows using the updated `Row` struct, maintaining consistency with the base parser behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.