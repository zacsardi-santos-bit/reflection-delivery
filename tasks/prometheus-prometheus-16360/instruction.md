Implement proper aggregation temporality support for histograms in the OTLP-to-Prometheus remote-write translation layer. Ensure that delta histograms are marked as gauge-type metrics and cumulative histograms as counter-type metrics. Add configuration options to control the acceptance of delta-temporality metrics and handle errors for invalid temporality.

*   Update the `exponentialToNativeHistogram` function:
    *   Accept `pmetric.AggregationTemporality` as the second parameter.
    *   Set the `ResetHint` field on the returned `prompb.Histogram`:
        *   Cumulative temporality: `prompb.Histogram_UNKNOWN`.
        *   Delta temporality: `prompb.Histogram_GAUGE`.

*   Update the `explicitHistogramToCustomBucketsHistogram` function:
    *   Accept `pmetric.AggregationTemporality` as the second parameter.
    *   Set the `ResetHint` field on the returned `prompb.Histogram`:
        *   Cumulative temporality: `prompb.Histogram_UNKNOWN`.
        *   Delta temporality: `prompb.Histogram_GAUGE`.

*   Modify the `PrometheusConverter` methods:
    *   `addExponentialHistogramDataPoints`:
        *   Accept `pmetric.AggregationTemporality` as the last parameter.
        *   Pass it to `exponentialToNativeHistogram`.
    *   `addCustomBucketsHistogramDataPoints`:
        *   Accept `pmetric.AggregationTemporality` as the last parameter.
        *   Pass it to `explicitHistogramToCustomBucketsHistogram`.

*   Update the `Settings` struct in `metrics_to_prw.go`:
    *   Add `AllowDeltaTemporality` as a `bool` field to control acceptance of delta-temporality metrics.

*   Enhance `FromMetrics` method:
    *   Validate aggregation temporality for each metric:
        *   Accept cumulative temporality always.
        *   Accept delta temporality only if `Settings.AllowDeltaTemporality` is true.
        *   Always reject unspecified temporality.
    *   Produce error messages:
        *   Invalid temporality: `invalid temporality and type combination for metric "<metric_name>"`.
        *   Unsupported type: `could not get aggregation temporality for <metric_name> as it has unsupported metric type Empty`.
    *   Implement partial-success semantics: convert valid metrics and return errors for invalid ones.

*   Ensure summary and gauge metric types are converted successfully regardless of the `AllowDeltaTemporality` setting.

*   Update the `NewAPI` function in `api.go`:
    *   Add `otlpNativeDeltaIngestion` as a boolean parameter for enabling native delta histogram ingestion.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.