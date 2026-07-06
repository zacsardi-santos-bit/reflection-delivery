I need to add an experimental option to the metrics package that lets developers specify which attribute keys are allowed by default when creating a metric instrument.

*   The metric/x package must provide a WithDefaultAttributes function that accepts zero or more attribute keys and returns a metric.InstrumentOption. This option, when passed during instrument creation, restricts which attribute keys are retained in recorded data points to only those specified.

*   When WithDefaultAttributes is called with one or more attribute keys and a measurement is recorded with additional attributes beyond those keys, the resulting data point must contain only the attributes whose keys were listed in WithDefaultAttributes.

*   When WithDefaultAttributes is called with no keys (empty argument list), all attributes on recorded measurements must be filtered out, producing data points with an empty attribute set.

*   When WithDefaultAttributes is not passed at instrument creation, all recorded attributes must be retained in data points (existing behavior is unchanged).

*   The per-instrument attribute filtering from WithDefaultAttributes must be overridden when a view explicitly matches the instrument (e.g., a view that matches all instruments with a default Stream). In that case, the view's attribute configuration takes precedence, and the full recorded attribute set appears in data points.

*   WithDefaultAttributes must work with all synchronous instrument types: Int64Counter, Float64Counter, Int64UpDownCounter, Float64UpDownCounter, Int64Gauge, Float64Gauge, Int64Histogram, and Float64Histogram.

*   WithDefaultAttributes must work with all asynchronous (observable) instrument types: Int64ObservableCounter, Float64ObservableCounter, Int64ObservableUpDownCounter, Float64ObservableUpDownCounter, Int64ObservableGauge, and Float64ObservableGauge.

*   The inserter.Instrument method in sdk/metric/pipeline.go must accept a new second parameter allowedKeys []attribute.Key inserted between the inst Instrument parameter and the readerAggregation Aggregation parameter. When allowedKeys is nil, behavior is unchanged. When allowedKeys is a non-nil slice, an attribute allow-keys filter is applied to the instrument stream.

*   The resolver.Aggregators method in sdk/metric/pipeline.go must accept a new second parameter allowedKeys []attribute.Key inserted after the id Instrument parameter. It must pass this value to each inserter.Instrument call.

*   The resolver.HistogramAggregators method in sdk/metric/pipeline.go must accept a new second parameter allowedKeys []attribute.Key inserted between the id Instrument parameter and the boundaries []float64 parameter. It must pass this value to each inserter.Instrument call.

*   The bridge/opencensus/test/go.mod and sdk/log/logtest/go.mod files must each add a replace directive pointing go.opentelemetry.io/otel/metric/x to the local metric/x directory.


*   Interface details: Type: Function
Name: WithDefaultAttributes
Location: metric/x/options.go
Signature: WithDefaultAttributes(keys ...attribute.Key) metric.InstrumentOption
Description: Returns a metric.InstrumentOption that specifies which attribute keys are allowed by default for a metric instrument. When the returned option is passed during instrument creation, only attributes whose keys appear in the provided list are included in recorded data points. Passing no keys results in all attributes being filtered out (empty attribute set). When allowedKeys is nil (the option was not provided), no filtering occurs. The returned option must also implement an AllowedKeys() []attribute.Key method, and an Experimental() method (to prevent panics when used with the standard API).

Type: Method
Name: Instrument
Location: sdk/metric/pipeline.go
Signature: Instrument(inst Instrument, allowedKeys []attribute.Key, readerAggregation Aggregation) ([]aggregate.Measure[N], error)
Description: Method on the generic inserter[N int64 | float64] type. Updated to accept a new second parameter allowedKeys []attribute.Key (inserted between inst and readerAggregation). When allowedKeys is nil, no attribute filtering is applied (previous behavior). When allowedKeys is a non-nil slice (even empty), an attribute allow-keys filter is applied to the stream for the instrument, permitting only the specified keys.

Type: Method
Name: Aggregators
Location: sdk/metric/pipeline.go
Signature: Aggregators(id Instrument, allowedKeys []attribute.Key) ([]aggregate.Measure[N], error)
Description: Method on the generic resolver[N int64 | float64] type. Updated to accept a new second parameter allowedKeys []attribute.Key (inserted after id). Passes allowedKeys through to each inserter's Instrument call.

Type: Method
Name: HistogramAggregators
Location: sdk/metric/pipeline.go
Signature: HistogramAggregators(id Instrument, allowedKeys []attribute.Key, boundaries []float64) ([]aggregate.Measure[N], error)
Description: Method on the generic resolver[N int64 | float64] type. Updated to accept a new second parameter allowedKeys []attribute.Key (inserted between id and boundaries). Passes allowedKeys through to each inserter's Instrument call.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.