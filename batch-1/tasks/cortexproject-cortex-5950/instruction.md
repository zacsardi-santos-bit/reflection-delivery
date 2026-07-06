Implement the ability to enforce series limits in Cortex's ingester based on specific label combinations. Configure per-labelset series limits to control the number of active series sharing specific label values. Reject write requests that exceed these limits and track active series per labelset with a Prometheus metric.

*   Add a new configuration type for per-labelset series limits:
    *   Define a struct `MaxSeriesPerLabelSet` in `pkg/util/validation/limits.go` with fields `Limit`, `LabelSet`, `Id`, and `Hash`.
    *   Ensure `Id` is set to the string representation of `LabelSet` and `Hash` is set to its FNV1a hash after deserialization.
    *   Include a slice field `MaxSeriesPerLabelSet` in the `Limits` struct, serialized as `max_series_per_label_set` in JSON/YAML.

*   Implement deserialization logic:
    *   Populate `Id` and `Hash` fields in `MaxSeriesPerLabelSet` during `UnmarshalJSON` and `UnmarshalYAML`.

*   Extend the `Overrides` type:
    *   Implement `MaxSeriesPerLabelSet(userID string) []MaxSeriesPerLabelSet` to return per-labelset limits for a user.

*   Enforce per-labelset series limits:
    *   Reject series pushes that exceed configured limits with an HTTP 400 error, including the `Id` of the violated labelset.

*   Track active series with a Prometheus metric:
    *   Register `cortex_ingester_active_series_per_labelset` in `pkg/ingester/metrics.go` with label dimensions `user` and `labelset`.
    *   Update this metric in `updateActiveSeries()` to reflect changes in active series counts per user and labelset.

*   Handle dynamic configuration changes:
    *   Remove metrics for labelsets no longer in configuration.
    *   Bootstrap counts for new limits from existing series data.

*   Ensure persistence across restarts:
    *   Maintain per-labelset active series counts and TSDB data to reflect correct metrics post-restart.

*   Update test helper function:
    *   Modify `prepareIngesterWithBlocksStorageAndLimits` in `pkg/ingester/ingester_test.go` to include a `tenantLimits validation.TenantLimits` parameter.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.