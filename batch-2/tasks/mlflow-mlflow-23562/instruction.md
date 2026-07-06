I'm working on the MLflow TypeScript tracing SDK and need to add support for storing traces in Unity Catalog tables on Databricks, in addition to the existing MLflow experiment storage.

*   The TraceLocationType enum must include a UC_TABLE_PREFIX variant to represent Unity Catalog table-prefix trace destinations.

*   The TraceLocation type must include an optional ucTablePrefix field with catalogName, schemaName, tablePrefix (all strings), and an optional otelSpansTableName string.

*   createTraceLocationFromUcTablePrefix must accept three strings (catalogName, schemaName, tablePrefix) and return a TraceLocation with type=UC_TABLE_PREFIX and a populated ucTablePrefix field.

*   isUcTraceLocation must return true for TraceLocation objects of type UC_TABLE_PREFIX, and false otherwise.

*   getUcLocationString must accept a UC TraceLocation and return a dot-separated string in the format 'catalogName.schemaName.tablePrefix'.

*   ucTablePrefixLocationString must accept the ucTablePrefix object directly and return 'catalogName.schemaName.tablePrefix'.

*   getOtelSpansTableName must return ucTablePrefix.otelSpansTableName if explicitly set, otherwise return '<catalog>.<schema>.<prefix>_otel_spans'.

*   parseTraceIdV4 must parse v4 trace IDs (format 'trace:/<location>/<otelTraceId>') and return a tuple [locationString, otelTraceId]. For non-v4 IDs (e.g., 'tr-<id>'), return [null, rawTraceId]. For structurally malformed v4 IDs (strings starting with 'trace:/' but missing required segments, or empty location), throw an error whose message matches /Invalid trace ID format/.

*   constructTraceIdV4 must accept (location: string, otelTraceId: string) and return 'trace:/<location>/<otelTraceId>'.

*   generateTraceIdV3 must accept an otelTraceId string and return 'tr-<otelTraceId>'.

*   ucLocationFromExperimentTags must accept a Record<string, string> of experiment tags and return null if the DATABRICKS_TRACE_DESTINATION_PATH_TAG is absent, if the path does not split into exactly three segments, or if any segment is empty. On success, return an object with catalogName, schemaName, tablePrefix, otelSpansTableName (from DATABRICKS_TRACE_SPAN_STORAGE_TABLE_TAG), otelLogsTableName (from DATABRICKS_TRACE_LOG_STORAGE_TABLE_TAG), and annotationsTableName (from DATABRICKS_TRACE_ANNOTATIONS_TABLE_TAG).

*   DATABRICKS_UC_TABLE_HEADER must be exported from the core constants module as a string constant representing the HTTP header name used to specify the Unity Catalog OTLP spans table.

*   MlflowClient.createTraceInfoV4 must POST the TraceInfo JSON directly as the request body (Databricks RPC convention — not wrapped in a key) to /api/4.0/mlflow/traces/${encodeURIComponent(location)}/${otelTraceId}/info, and return a TraceInfo parsed from the JSON response with ucTablePrefix.otelSpansTableName populated if the backend returns it.

*   MlflowClient.exportOtlpSpansToUc must resolve immediately with undefined when passed an empty spans array. When spans are present, it must create an OTLPTraceExporter with url='${host}/api/2.0/otel/v1/traces', headers containing DATABRICKS_UC_TABLE_HEADER set to the tableName argument and Authorization set to 'Bearer <token>', then export all spans.

*   DatabricksUCTableSpanExporter must be constructable from a MlflowClient instance.

*   DatabricksUCTableSpanProcessor must be constructable from a DatabricksUCTableSpanExporter and a location object { catalogName, schemaName, tablePrefix }. On onStart, it must register the trace with a v4 trace ID starting with 'trace:/<catalog>.<schema>.<prefix>/' and set TraceMetadataKey.SCHEMA_VERSION to '4'. After onEnd and forceFlush, it must call createTraceInfoV4 with the full trace info (including tags set via updateCurrentTrace), then exportOtlpSpansToUc using the otelSpansTableName returned by the backend.

*   When the traceLocation option ({ catalogName, schemaName, tablePrefix }) is provided to init(), the tracing pipeline must wire up a DatabricksUCTableSpanProcessor, causing all spans to be routed to the V4 endpoint with the configured UC location. The resulting trace ID must start with 'trace:/<catalog>.<schema>.<prefix>/', and the V4 request body must include trace_location.uc_table_prefix with catalog_name, schema_name, and table_prefix.

*   TraceInfo.toJson() must serialize a UC_TABLE_PREFIX TraceLocation as { type: TraceLocationType.UC_TABLE_PREFIX, uc_table_prefix: { catalog_name, schema_name, table_prefix } }. TraceInfo.fromJson() must deserialize this back, populating traceLocation.ucTablePrefix with catalogName, schemaName, and tablePrefix.


*   Interface details: Type: Enum Value
Name: TraceLocationType.UC_TABLE_PREFIX
Location: libs/typescript/core/src/core/entities/trace_location.ts
Description: New enum variant added to the existing TraceLocationType enum, representing a Unity Catalog table-prefix trace destination.

Type: Function
Name: createTraceLocationFromUcTablePrefix
Location: libs/typescript/core/src/core/entities/trace_location.ts
Signature: createTraceLocationFromUcTablePrefix(catalogName: string, schemaName: string, tablePrefix: string): TraceLocation
Description: Constructs a TraceLocation object of type UC_TABLE_PREFIX with a ucTablePrefix field containing catalogName, schemaName, and tablePrefix.

Type: Function
Name: isUcTraceLocation
Location: libs/typescript/core/src/core/entities/trace_location.ts
Signature: isUcTraceLocation(loc: TraceLocation): boolean
Description: Returns true when the given TraceLocation is of type UC_TABLE_PREFIX.

Type: Function
Name: getUcLocationString
Location: libs/typescript/core/src/core/entities/trace_location.ts
Signature: getUcLocationString(loc: TraceLocation): string | null
Description: Returns a dot-separated string 'catalog.schema.prefix' for a UC table-prefix TraceLocation, or null if the location is not of UC_TABLE_PREFIX type or has no ucTablePrefix.

Type: Function
Name: ucTablePrefixLocationString
Location: libs/typescript/core/src/core/entities/trace_location.ts
Signature: ucTablePrefixLocationString(ucTablePrefix: { catalogName: string; schemaName: string; tablePrefix: string }): string
Description: Returns a dot-separated string 'catalog.schema.prefix' from a ucTablePrefix object directly.

Type: Function
Name: getOtelSpansTableName
Location: libs/typescript/core/src/core/entities/trace_location.ts
Signature: getOtelSpansTableName(loc: TraceLocation): string | null
Description: Returns the OTLP spans table name for a UC location, or null for non-UC locations. If ucTablePrefix.otelSpansTableName is explicitly set, returns that value; otherwise returns '<catalog>.<schema>.<prefix>_otel_spans'. Returns null if the location has no tablePrefix set.

Type: Type Extension
Name: TraceLocation
Location: libs/typescript/core/src/core/entities/trace_location.ts
Description: Existing TraceLocation type extended with an optional ucTablePrefix field of type UnityCatalogLocation { catalogName: string; schemaName: string; tablePrefix?: string; otelSpansTableName?: string; otelLogsTableName?: string; annotationsTableName?: string }. The toJson()/fromJson() serialization of TraceInfo must support a uc_table_prefix field in snake_case: { catalog_name, schema_name, table_prefix, otel_spans_table_name?, otel_logs_table_name?, annotations_table_name? }.

Type: Module (new)
Name: trace_id utilities
Location: libs/typescript/core/src/core/utils/trace_id.ts
Description: New module exporting three functions for working with MLflow trace IDs.

Type: Function
Name: parseTraceIdV4
Location: libs/typescript/core/src/core/utils/trace_id.ts
Signature: parseTraceIdV4(traceId: string): [string | null, string]
Description: Parses a v4 trace ID string (format 'trace:/<location>/<otelTraceId>'). Returns [locationString, otelTraceId]. For non-v4 strings (e.g. 'tr-<id>'), returns [null, rawTraceId]. For strings that begin with 'trace:/' but are structurally malformed (e.g., only one path segment after the prefix, or an empty location segment), throws an error whose message matches /Invalid trace ID format/.

Type: Function
Name: constructTraceIdV4
Location: libs/typescript/core/src/core/utils/trace_id.ts
Signature: constructTraceIdV4(location: string, otelTraceId: string): string
Description: Returns a v4 trace ID string: 'trace:/<location>/<otelTraceId>'.

Type: Function
Name: generateTraceIdV3
Location: libs/typescript/core/src/core/utils/trace_id.ts
Signature: generateTraceIdV3(otelTraceId: string): string
Description: Returns a v3 trace ID string: 'tr-<otelTraceId>'.

Type: Module (new)
Name: destination utilities
Location: libs/typescript/core/src/core/destination.ts
Description: New module exporting constants and a function for resolving Unity Catalog trace destinations from experiment tags.

Type: Constant
Name: DATABRICKS_TRACE_DESTINATION_PATH_TAG
Location: libs/typescript/core/src/core/destination.ts
Description: Experiment tag key string for the UC destination path (format 'catalog.schema.prefix').

Type: Constant
Name: DATABRICKS_TRACE_SPAN_STORAGE_TABLE_TAG
Location: libs/typescript/core/src/core/destination.ts
Description: Experiment tag key string for the OTLP spans storage table name.

Type: Constant
Name: DATABRICKS_TRACE_LOG_STORAGE_TABLE_TAG
Location: libs/typescript/core/src/core/destination.ts
Description: Experiment tag key string for the OTLP logs storage table name.

Type: Constant
Name: DATABRICKS_TRACE_ANNOTATIONS_TABLE_TAG
Location: libs/typescript/core/src/core/destination.ts
Description: Experiment tag key string for the annotations table name.

Type: Function
Name: ucLocationFromExperimentTags
Location: libs/typescript/core/src/core/destination.ts
Signature: ucLocationFromExperimentTags(tags: Record<string, string>): { catalogName: string; schemaName: string; tablePrefix: string; otelSpansTableName: string; otelLogsTableName: string; annotationsTableName: string } | null
Description: Reads DATABRICKS_TRACE_DESTINATION_PATH_TAG from tags, splits by '.', and returns null if the tag is absent, the path doesn't have exactly three segments, or any segment is empty. On success, returns an object with catalogName, schemaName, tablePrefix (from the path), otelSpansTableName (from DATABRICKS_TRACE_SPAN_STORAGE_TABLE_TAG), otelLogsTableName (from DATABRICKS_TRACE_LOG_STORAGE_TABLE_TAG), and annotationsTableName (from DATABRICKS_TRACE_ANNOTATIONS_TABLE_TAG).

Type: Constant
Name: DATABRICKS_UC_TABLE_HEADER
Location: libs/typescript/core/src/core/constants.ts
Description: HTTP header name string used to specify the Unity Catalog OTLP spans table when exporting spans. Added to the existing constants module.

Type: Method
Name: createTraceInfoV4
Location: libs/typescript/core/src/clients/client.ts
Signature: createTraceInfoV4(location: string, otelTraceId: string, traceInfo: TraceInfo): Promise<TraceInfo>
Description: Method added to MlflowClient. POSTs the TraceInfo JSON directly as the request body (Databricks RPC convention — the body IS the trace info object, not wrapped in a key) to /api/4.0/mlflow/traces/${encodeURIComponent(location)}/${otelTraceId}/info. Returns a TraceInfo parsed from the JSON response, with ucTablePrefix.otelSpansTableName populated if the backend returns it.

Type: Method
Name: exportOtlpSpansToUc
Location: libs/typescript/core/src/clients/client.ts
Signature: exportOtlpSpansToUc(spans: ReadableSpan[], tableName: string): Promise<void>
Description: Method added to MlflowClient. If spans is empty, resolves immediately with undefined (no network call). Otherwise creates an OTLPTraceExporter configured with url = '${host}/api/2.0/otel/v1/traces', headers including DATABRICKS_UC_TABLE_HEADER set to tableName and Authorization set to 'Bearer <token>', then exports the spans. Returns Promise<void>.

Type: Module (new)
Name: uc_table exporter/processor
Location: libs/typescript/core/src/exporters/uc_table.ts
Description: New module exporting DatabricksUCTableSpanExporter and DatabricksUCTableSpanProcessor.

Type: Class
Name: DatabricksUCTableSpanExporter
Location: libs/typescript/core/src/exporters/uc_table.ts
Description: Wraps MlflowClient to export spans and trace info to Unity Catalog. Constructor: DatabricksUCTableSpanExporter(client: MlflowClient). Implements the SpanExporter interface with export(), forceFlush(), and shutdown() methods. When export() is called with root spans, it asynchronously calls createTraceInfoV4 and exportOtlpSpansToUc on the client. forceFlush() awaits all pending export operations before resolving.

Type: Class
Name: DatabricksUCTableSpanProcessor
Location: libs/typescript/core/src/exporters/uc_table.ts
Description: Span processor that assigns v4 trace IDs and routes trace data to Unity Catalog. Constructor: DatabricksUCTableSpanProcessor(exporter: DatabricksUCTableSpanExporter, location: { catalogName: string; schemaName: string; tablePrefix: string }). Implements onStart(span, context), onEnd(span), forceFlush(), and shutdown(). On onStart for root spans: registers the trace with InMemoryTraceManager using a v4 trace ID (format 'trace:/<catalog>.<schema>.<prefix>/<otelTraceId>') and sets TraceMetadataKey.SCHEMA_VERSION to '4'. On onEnd for root spans: updates trace state/duration and calls the exporter. forceFlush() delegates to the exporter's forceFlush(). After onEnd + forceFlush, createTraceInfoV4 is called with tags/metadata preserved (including tags set via updateCurrentTrace), and exportOtlpSpansToUc is called using the otelSpansTableName returned by the backend.

Type: Config Extension
Name: MLflowTracingConfig.traceLocation
Location: libs/typescript/core/src/core/config.ts
Description: The MLflowTracingConfig type must be extended with an optional traceLocation field: { catalogName: string; schemaName: string; tablePrefix: string }. When traceLocation is provided to init(), a DatabricksUCTableSpanProcessor is wired into the tracing pipeline, routing all spans to the V4 endpoint with the configured UC location. The resulting trace IDs must start with 'trace:/<catalog>.<schema>.<prefix>/', and the V4 request body must include trace_location.uc_table_prefix with catalog_name, schema_name, and table_prefix.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.