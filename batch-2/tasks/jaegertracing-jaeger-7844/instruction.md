I'm working on the Cassandra storage backend in Jaeger and need to remove an unnecessary intermediate step from the trace conversion pipeline.

*   The value type constants StringType, BoolType, Int64Type, Float64Type, and BinaryType in the Cassandra DB model package must be exported (publicly accessible uppercase names with string values 'string', 'bool', 'int64', 'float64', 'binary' respectively). Previously these were unexported lowercase identifiers.

*   The span reference type constants FollowsFrom (value 'follows-from') and ChildOf (value 'child-of') must be exported from the Cassandra DB model package. They are used as the value for the RefType field in SpanRef structs.

*   The Timestamp field in the dbmodel.Log struct must be of type int64 representing epoch microseconds (not time.Time). Code that converts log timestamps to OTel span event timestamps must multiply by 1000 to convert microseconds to nanoseconds; the reverse is verifiable as int64(event.Timestamp()/1000) == log.Timestamp.

*   FromDBModel must accept []dbmodel.Span and return ptrace.Traces with no error return value. An empty input slice must produce traces with 0 ResourceSpans. A single dbmodel.Span with an empty Process must produce 1 ResourceSpan with 0 resource attributes.

*   ToDBModel must accept ptrace.Traces and return []dbmodel.Span. It must return an empty slice when traces contain no resource spans, or when resource spans contain no scope spans. A span with empty attributes must produce a dbmodel.Span with no Tags and a Process whose ServiceName is the noServiceName constant value.

*   dbSpansToSpans must accept []dbmodel.Span and a ptrace.ResourceSpansSlice and populate the slice such that a single-element input produces exactly 1 entry in the ResourceSpansSlice.

*   dbLogsToSpanEvents must accept []dbmodel.Log (with Timestamp as int64 microseconds) and ptrace.SpanEventSlice, setting each event's timestamp by multiplying the microsecond value by 1000 to produce nanoseconds.

*   dbTagsToAttributes must accept []dbmodel.KeyValue and pcommon.Map, converting each tag using the exported type constants. For a KeyValue with an unrecognized ValueType string, it must produce a string attribute with value formatted as: <Unknown Jaeger TagType "TYPE_VALUE"> where TYPE_VALUE is the actual ValueType string.

*   setSpanStatus must accept pcommon.Map and ptrace.Span and set span status from the attributes map. This function replaces the previous setInternalSpanStatus with identical observable behavior.

*   spanToDbSpan must accept ptrace.Span, pcommon.InstrumentationScope, and dbmodel.Process, returning a dbmodel.Span. Span link references must populate the Refs field of the returned dbmodel.Span using dbmodel.FollowsFrom as the RefType string value. A span with no attributes must produce a dbmodel.Span with empty Tags.

*   resourceToDbProcess must accept pcommon.Resource and return dbmodel.Process. When the resource contains no service name attribute, the returned Process.ServiceName must equal the noServiceName constant. When only a service name attribute is present, the returned Process.ServiceName must equal its value.

*   appendTagsFromAttributes must accept []dbmodel.KeyValue as an accumulator and pcommon.Map, appending new entries with ValueType set using the exported dbmodel type constants. Map-type OTel attributes must be serialized as JSON strings with ValueType dbmodel.StringType.

*   getTagFromStatusCode must return dbmodel.KeyValue{Key: tagError, ValueType: dbmodel.BoolType, ValueBool: true} (where tagError = "error") when called with StatusCodeError. For StatusCodeOk it must return dbmodel.KeyValue{Key: otelsemconv.OtelStatusCode, ValueType: dbmodel.StringType, ValueString: statusOk}. It must return false (no tag) for StatusCodeUnset.

*   getTagFromStatusMsg must return a dbmodel.KeyValue with ValueType dbmodel.StringType and ValueString set to the message, along with true, for a non-empty message string. It must return false for an empty string.

*   The fixture file internal/storage/v2/cassandra/tracestore/fixtures/cas_01.json must be updated to dbmodel.Span JSON format (keys matching Go exported struct field names: TraceID, SpanID, OperationName, Tags, Logs, Refs, Process, ServiceName, SpanHash, StartTime, Duration, Flags, ParentID). Tags use 'Key', 'ValueType' (string: 'string'/'bool'/'int64'/'float64'/'binary'), and the value field. Log Timestamp is int64 microseconds. SpanRef RefType is a plain string ('child-of' or 'follows-from'). The fixture file internal/storage/v2/cassandra/tracestore/fixtures/otel_traces_01.json must also be updated to reflect any new fields produced by the conversion (such as span flags, status message, and trace state).


*   Interface details: ## Constants in `internal/storage/v1/cassandra/spanstore/dbmodel`

The following constants must be exported (uppercase) from the `dbmodel` package. They represent value types for the `KeyValue.ValueType` field and span reference types:

Type: Constant
Name: StringType
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Value: "string"
Description: Exported constant for string value type (was previously unexported as stringType).

Type: Constant
Name: BoolType
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Value: "bool"
Description: Exported constant for bool value type (was previously unexported as boolType).

Type: Constant
Name: Int64Type
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Value: "int64"
Description: Exported constant for int64 value type (was previously unexported as int64Type).

Type: Constant
Name: Float64Type
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Value: "float64"
Description: Exported constant for float64 value type (was previously unexported as float64Type).

Type: Constant
Name: BinaryType
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Value: "binary"
Description: Exported constant for binary value type (was previously unexported as binaryType).

Type: Constant
Name: FollowsFrom
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Value: "follows-from"
Description: Exported constant for the follows-from span reference type (was previously unexported as followsFrom). Used in SpanRef.RefType field.

Type: Constant
Name: ChildOf
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Value: "child-of"
Description: Exported constant for the child-of span reference type (was previously unexported as childOf). Used in SpanRef.RefType field and in filtering references that match the parent span. Also used in internal reference filtering logic (e.g., skip the parent span reference when constructing span links).

---

## Data Structure Change in `internal/storage/v1/cassandra/spanstore/dbmodel`

Type: StructField
Name: Timestamp
Struct: Log
Location: internal/storage/v1/cassandra/spanstore/dbmodel/model.go
Type: int64
Description: The Log.Timestamp field must be int64 (epoch microseconds), not time.Time. Tests create log entries with int64 timestamps and verify span events using int64(event.Timestamp()/1000) == log.Timestamp.

---

## Public Functions in `internal/storage/v2/cassandra/tracestore`

Type: Function
Name: FromDBModel
Location: internal/storage/v2/cassandra/tracestore/from_dbmodel.go
Signature: FromDBModel(spans []dbmodel.Span) ptrace.Traces
Description: Converts a slice of Cassandra DB model spans into OTel ptrace.Traces. Returns no error. An empty slice returns traces with 0 ResourceSpans. A single span with an empty Process produces 1 ResourceSpan with 0 resource attributes.

Type: Function
Name: ToDBModel
Location: internal/storage/v2/cassandra/tracestore/to_dbmodel.go
Signature: ToDBModel(td ptrace.Traces) []dbmodel.Span
Description: Converts OTel ptrace.Traces to a slice of dbmodel.Span. Returns an empty slice when there are no resource spans or when resource spans have no scope spans. For empty span attributes, the resulting span has no Tags and the Process uses noServiceName as ServiceName.

---

## Private Functions in `internal/storage/v2/cassandra/tracestore`

These unexported functions are called directly from test files within the same package.

Type: Function
Name: dbSpansToSpans
Location: internal/storage/v2/cassandra/tracestore/from_dbmodel.go
Signature: dbSpansToSpans(spans []dbmodel.Span, rss ptrace.ResourceSpansSlice)
Description: Populates a ptrace.ResourceSpansSlice from a slice of dbmodel.Span. Each span produces one entry in the ResourceSpansSlice.

Type: Function
Name: dbLogsToSpanEvents
Location: internal/storage/v2/cassandra/tracestore/from_dbmodel.go
Signature: dbLogsToSpanEvents(logs []dbmodel.Log, events ptrace.SpanEventSlice)
Description: Converts []dbmodel.Log to ptrace.SpanEventSlice. Log.Timestamp is int64 microseconds; span event timestamps are set in nanoseconds (microseconds * 1000). Verifiable as: int64(event.Timestamp()/1000) == log.Timestamp.

Type: Function
Name: dbTagsToAttributes
Location: internal/storage/v2/cassandra/tracestore/from_dbmodel.go
Signature: dbTagsToAttributes(tags []dbmodel.KeyValue, attributes pcommon.Map)
Description: Converts []dbmodel.KeyValue to OTel pcommon.Map using exported type constants (StringType, BoolType, Int64Type, Float64Type, BinaryType). For an unknown ValueType string, produces a string attribute with value formatted as: <Unknown Jaeger TagType "TYPE_VALUE">.

Type: Function
Name: setSpanStatus
Location: internal/storage/v2/cassandra/tracestore/from_dbmodel.go
Signature: setSpanStatus(attrs pcommon.Map, span ptrace.Span)
Description: Sets span status from attributes. Replaces the previous setInternalSpanStatus function with identical behavior.

Type: Function
Name: spanToDbSpan
Location: internal/storage/v2/cassandra/tracestore/to_dbmodel.go
Signature: spanToDbSpan(span ptrace.Span, scope pcommon.InstrumentationScope, process dbmodel.Process) dbmodel.Span
Description: Converts an OTel span and scope to a dbmodel.Span. Link references populate the Refs field using dbmodel.FollowsFrom as the RefType string. Takes a dbmodel.Process parameter (not derived internally). Returns dbmodel.Span with Tags empty when span has no attributes.

Type: Function
Name: resourceToDbProcess
Location: internal/storage/v2/cassandra/tracestore/to_dbmodel.go
Signature: resourceToDbProcess(resource pcommon.Resource) dbmodel.Process
Description: Converts an OTel pcommon.Resource to dbmodel.Process. When no service name attribute is present, uses the noServiceName constant as ServiceName.

Type: Function
Name: appendTagsFromAttributes
Location: internal/storage/v2/cassandra/tracestore/to_dbmodel.go
Signature: appendTagsFromAttributes(tags []dbmodel.KeyValue, attrs pcommon.Map) []dbmodel.KeyValue
Description: Appends OTel pcommon.Map entries to a []dbmodel.KeyValue accumulator, using dbmodel type constants for ValueType. Map-type attributes are serialized to JSON string representation.

Type: Function
Name: getTagFromStatusCode
Location: internal/storage/v2/cassandra/tracestore/to_dbmodel.go
Signature: getTagFromStatusCode(code ptrace.StatusCode) (dbmodel.KeyValue, bool)
Description: Returns a dbmodel.KeyValue and true for StatusCodeOk (KeyValue with Key=otelsemconv.OtelStatusCode, ValueType=dbmodel.StringType, ValueString=statusOk) and StatusCodeError (KeyValue with Key=tagError, ValueType=dbmodel.BoolType, ValueBool=true). Returns an empty KeyValue and false for StatusCodeUnset.

Type: Function
Name: getTagFromStatusMsg
Location: internal/storage/v2/cassandra/tracestore/to_dbmodel.go
Signature: getTagFromStatusMsg(msg string) (dbmodel.KeyValue, bool)
Description: Returns a dbmodel.KeyValue (with ValueType=dbmodel.StringType and ValueString set to msg) and true for a non-empty message string. Returns an empty KeyValue and false for an empty string.

---

## Private Constants in `internal/storage/v2/cassandra/tracestore`

Type: Constant
Name: noServiceName
Location: internal/storage/v2/cassandra/tracestore/to_dbmodel.go
Description: Unexported string constant used as the default service name when no service name attribute is found in OTel resource attributes. Referenced in tests as: process.ServiceName == noServiceName.

Type: Constant
Name: tagError
Location: internal/storage/v2/cassandra/tracestore/to_dbmodel.go
Description: Unexported string constant used as the Key field in the error KeyValue tag. For StatusCodeError, getTagFromStatusCode returns dbmodel.KeyValue{Key: tagError, ValueType: dbmodel.BoolType, ValueBool: true}. The constant value is "error".

---

## Fixture Files in `internal/storage/v2/cassandra/tracestore/fixtures`

The fixture files used by round-trip tests must be updated to match the new data format. These are not test files but data files that tests read:

File: internal/storage/v2/cassandra/tracestore/fixtures/cas_01.json
Description: This fixture must be in dbmodel.Span JSON format (not Jaeger protobuf batch format). The JSON keys must match the exported Go struct field names of dbmodel.Span (e.g., "TraceID", "SpanID", "OperationName", "Tags", "Logs", "Refs", "Process", "ServiceName", "SpanHash", "StartTime", "Duration", "Flags", "ParentID"). Tags use "Key", "ValueType" (string like "string"/"bool"/"int64"/"float64"/"binary"), and the appropriate value field. Log entries use "Timestamp" (int64 microseconds) and "Fields". SpanRefs use "RefType" (string like "child-of"/"follows-from"), "TraceID", "SpanID". Process uses "ServiceName" and "Tags".

File: internal/storage/v2/cassandra/tracestore/fixtures/otel_traces_01.json
Description: This fixture stores expected OTel trace JSON. It may need updating if the conversion logic changes which fields are populated (e.g., span flags, status message, trace state).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.