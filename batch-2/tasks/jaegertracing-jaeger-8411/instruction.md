I'm working on adding metrics support to the ClickHouse storage backend in Jaeger.

*   A NewReader constructor must be provided in the internal/storage/v2/clickhouse/metricstore package that accepts a ClickHouse driver connection (driver.Conn) and returns a *Reader

*   The Reader's GetLatencies method must return a *metrics.MetricFamily named "service_latencies" when GroupByOperation is false; each Metric in the family must have exactly one label named "service_name" with the service name as its value, and MetricPoints holding gauge double values

*   When GetLatencies is called with GroupByOperation set to true, the returned MetricFamily must be named "service_operation_latencies"; each Metric must have exactly two labels in order: first "service_name", then "operation"

*   GetLatencies must return an error containing the string "failed to query latencies" when the underlying database query fails, an error containing "failed to scan metrics row" when row scanning fails, and an error containing "error iterating metrics rows" when row iteration encounters an error

*   GetCallRates, GetErrorRates, and GetMinStepDuration must each return a package-level sentinel error variable named errNotImplemented

*   The unexported function stepSeconds must accept a metricstore.BaseQueryParameters value and return uint64: a nil, zero, or negative Step field returns defaultStepSeconds; a positive sub-second Step returns 1; a Step value with fractional seconds is truncated to whole seconds

*   The package-level constant defaultStepSeconds must be of type uint64

*   The unexported function convertSpanKinds must accept a []string of span kind names and return []string: "SPAN_KIND_SERVER" maps to "server", "SPAN_KIND_CLIENT" maps to "client", "SPAN_KIND_UNSPECIFIED" maps to empty string "", and unknown/unrecognized kinds are omitted from the output

*   SQL query string constants named SelectLatencies and SelectLatenciesByOperation must be defined in the internal/storage/v2/clickhouse/sql package (in queries.go)

*   An unexported struct named metricsRow must be defined in the internal/storage/v2/clickhouse/metricstore package with the following exported fields: Timestamp (time.Time), ServiceName (string), Operation (string), and Value (float64)


*   Interface details: Type: Function
Name: NewReader
Location: internal/storage/v2/clickhouse/metricstore/reader.go
Signature: NewReader(conn driver.Conn) *Reader
Description: Constructor for the ClickHouse metric store reader. driver.Conn is from github.com/ClickHouse/clickhouse-go/v2/lib/driver.

Type: Struct
Name: Reader
Location: internal/storage/v2/clickhouse/metricstore/reader.go
Description: ClickHouse metric store reader. Must satisfy the metricstore.Reader interface from internal/storage/v1/api/metricstore.
Signature:
  GetLatencies(ctx context.Context, params *metricstore.LatenciesQueryParameters) (*metrics.MetricFamily, error)
  GetCallRates(ctx context.Context, params *metricstore.CallRateQueryParameters) (*metrics.MetricFamily, error)
  GetErrorRates(ctx context.Context, params *metricstore.ErrorRateQueryParameters) (*metrics.MetricFamily, error)
  GetMinStepDuration(ctx context.Context, params *metricstore.MinStepDurationQueryParameters) (time.Duration, error)

Type: Variable
Name: errNotImplemented
Location: internal/storage/v2/clickhouse/metricstore/reader.go
Description: Package-level (unexported) sentinel error returned by GetCallRates, GetErrorRates, and GetMinStepDuration.

Type: Constant
Name: defaultStepSeconds
Location: internal/storage/v2/clickhouse/metricstore/reader.go
Description: Unexported package-level constant of type uint64. Used as the fallback step size in seconds when the query parameters do not specify a valid step.

Type: Function
Name: stepSeconds
Location: internal/storage/v2/clickhouse/metricstore/reader.go
Signature: stepSeconds(p metricstore.BaseQueryParameters) uint64
Description: Unexported helper. Converts BaseQueryParameters.Step to uint64 seconds. Returns defaultStepSeconds when Step is nil, zero, or negative. Clamps positive sub-second values to 1. Truncates fractional seconds.

Type: Function
Name: convertSpanKinds
Location: internal/storage/v2/clickhouse/metricstore/reader.go
Signature: convertSpanKinds(kinds []string) []string
Description: Unexported helper. Converts OpenTelemetry span kind strings to ClickHouse-compatible lowercase strings. "SPAN_KIND_SERVER" -> "server", "SPAN_KIND_CLIENT" -> "client", "SPAN_KIND_UNSPECIFIED" -> "" (empty string, included in output). Unknown/unrecognized kinds are skipped (not included in output).

Type: Struct
Name: metricsRow
Location: internal/storage/v2/clickhouse/metricstore/row.go
Description: Unexported struct representing a single row from a SPM aggregation query. Fields (must be exactly these names and types):
  Timestamp   time.Time
  ServiceName string
  Operation   string
  Value       float64

Type: Constant
Name: SelectLatencies
Location: internal/storage/v2/clickhouse/sql/queries.go
Description: Exported string constant holding the SQL query for fetching per-service latency metrics (no operation grouping).

Type: Constant
Name: SelectLatenciesByOperation
Location: internal/storage/v2/clickhouse/sql/queries.go
Description: Exported string constant holding the SQL query for fetching per-service-and-operation latency metrics.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.