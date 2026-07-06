I'm working on the Cassandra storage backend for Jaeger.

*   A GetSpanKind function must be added to the Cassandra spanstore database model package. It accepts a *Span and returns the ValueString of the first tag whose Key equals the span kind key constant, or an empty string if no such tag exists.

*   A CoreSpanWriter interface must be defined in the v1 Cassandra spanstore package (internal/storage/v1/cassandra/spanstore/writer.go) with two methods: WriteSpan(ds *dbmodel.Span) error and Close() error. The existing SpanWriter type must implement this interface (enforced by a compile-time var _ CoreSpanWriter = &SpanWriter{} declaration).

*   The SpanWriter.WriteSpan method signature must change from accepting (context.Context, *model.Span) to accepting only (*dbmodel.Span) — the context parameter is removed entirely.

*   A mock type named CoreSpanWriter must be added to internal/storage/v1/cassandra/spanstore/mocks/mocks.go (the existing mocks file). It must implement WriteSpan(ds *dbmodel.Span) error and Close() error using the testify/mock pattern, consistent with the existing CoreSpanReader mock in the same file.

*   A new file internal/storage/v2/cassandra/tracestore/writer.go must define a TraceWriter struct with an unexported field named exactly 'writer' of type spanstore.CoreSpanWriter. The struct must implement tracestore.Writer.

*   TraceWriter must have a WriteTraces(_ context.Context, td ptrace.Traces) error method that calls ToDBModel(td) to get a []dbmodel.Span, iterates over the slice, calls writer.WriteSpan(&dbSpans[i]) for each, and collects all errors using errors.Join — producing a combined error whose string representation joins individual error messages with a newline character ('\n').

*   TraceWriter must have a Close() error method that delegates to writer.Close().

*   A NewTraceWriter constructor must exist at internal/storage/v2/cassandra/tracestore/writer.go with signature: NewTraceWriter(session cassandra.Session, writeCacheTTL time.Duration, metricsFactory metrics.Factory, logger *zap.Logger, options ...spanstore.Option) (*TraceWriter, error). It must return an error containing the string 'neither table operation_names_v2 nor operation_names exist' when neither the operation_names_v2 nor the operation_names Cassandra table is accessible.

*   A writerOptions function must exist in internal/storage/v2/cassandra/factory.go with signature writerOptions(opts *cassandra.Options) ([]cspanstore.Option, error). When only a tag blacklist (TagBlackList) is configured with Tags=false, it must return a slice of exactly 1 option and no error. When only a tag whitelist (TagWhiteList) is configured with Tags=true, it must return a slice of exactly 1 option and no error. When both are set simultaneously, it must return an error containing 'only one of TagIndexBlacklist and TagIndexWhitelist can be specified'.

*   Factory.CreateTraceWriter in the v2 Cassandra package must use writerOptions to build options before constructing the writer. It must propagate the 'only one of TagIndexBlacklist and TagIndexWhitelist can be specified' error from writerOptions and the 'neither table operation_names_v2 nor operation_names exist' error from NewTraceWriter.

*   The CreateSpanReader method that previously returned a 'not implemented' error must be removed from the v1 Cassandra factory (internal/storage/v1/cassandra/factory.go). The writerOptions function must also be removed from the v1 factory since it is now located in the v2 factory.

*   The firehose flag check (span.Flags.IsFirehoseEnabled()) in SpanWriter.writeIndexes must be removed; all spans are indexed regardless of a firehose flag.


*   Interface details: Type: Function
Name: GetSpanKind
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Signature: GetSpanKind(ds *Span) string
Description: Searches ds.Tags for a tag whose Key equals model.SpanKindKey and returns its ValueString. Returns empty string if no such tag is found.

---

Type: Interface
Name: CoreSpanWriter
Location: internal/storage/v1/cassandra/spanstore/writer.go
Description: Write-only interface for the Cassandra span writer. SpanWriter must satisfy this interface (verified via compile-time check var _ CoreSpanWriter = &SpanWriter{}).
Methods:
  WriteSpan(ds *dbmodel.Span) error
  Close() error

---

Type: Method (signature change on existing type)
Name: SpanWriter.WriteSpan
Location: internal/storage/v1/cassandra/spanstore/writer.go
Signature: WriteSpan(ds *dbmodel.Span) error
Description: Writes a span to Cassandra. The context.Context parameter is removed; the method now accepts only *dbmodel.Span.

---

Type: Struct (Mock)
Name: CoreSpanWriter
Location: internal/storage/v1/cassandra/spanstore/mocks/mocks.go
Description: Testify mock implementing the CoreSpanWriter interface. Added to the existing mocks file alongside the existing CoreSpanReader mock.
Methods:
  WriteSpan(ds *dbmodel.Span) error
  Close() error

---

Type: Function
Name: NewTraceWriter
Location: internal/storage/v2/cassandra/tracestore/writer.go
Signature: NewTraceWriter(session cassandra.Session, writeCacheTTL time.Duration, metricsFactory metrics.Factory, logger *zap.Logger, options ...spanstore.Option) (*TraceWriter, error)
Description: Constructs a TraceWriter backed by a new SpanWriter. Returns an error containing "neither table operation_names_v2 nor operation_names exist" when neither Cassandra operation table is accessible.

---

Type: Struct
Name: TraceWriter
Location: internal/storage/v2/cassandra/tracestore/writer.go
Description: Implements tracestore.Writer. Converts ptrace.Traces to dbmodel spans and writes them using the underlying CoreSpanWriter. The unexported field is named exactly "writer".
Fields:
  writer spanstore.CoreSpanWriter  (unexported; exact field name required for in-package tests)
Methods:
  WriteTraces(ctx context.Context, td ptrace.Traces) error
  Close() error

---

Type: Function
Name: ToDBModel
Location: internal/storage/v2/cassandra/tracestore/ (existing package)
Signature: ToDBModel(td ptrace.Traces) []dbmodel.Span
Description: Converts an OpenTelemetry ptrace.Traces value into a slice of Cassandra dbmodel.Span. Already exists for reading; also used by TraceWriter.WriteTraces.

---

Type: Function
Name: writerOptions
Location: internal/storage/v2/cassandra/factory.go
Signature: writerOptions(opts *cassandra.Options) ([]cspanstore.Option, error)
Description: Builds the list of SpanWriter options from the given cassandra.Options. Returns nil slice and no error when no tag filters are needed. Returns a slice of length 1 when exactly one filter type (blacklist or whitelist) is configured. Returns an error containing "only one of TagIndexBlacklist and TagIndexWhitelist can be specified" when both are set simultaneously. This function is moved from the v1 cassandra factory to the v2 cassandra factory.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.