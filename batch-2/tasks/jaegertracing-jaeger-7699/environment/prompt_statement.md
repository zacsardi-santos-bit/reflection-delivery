I'm cleaning up the Cassandra storage backend for Jaeger and the span writer is annoyingly coupled to an external tracing library for all its core types (spans, trace IDs, processes, tags, logs, references) and I want it working directly against our Cassandra-native internal model instead. Those internal types use primitive integers for timestamps, durations, and IDs, they represent a process as a value type rather than a pointer, and they use a different field name for span references, so the writer should just accept the internal db model span type directly with no conversion from the external types. Oh and the write method takes a context param it never actually uses, so drop that while you're in there and simplify the interface down to the core write and close ops.

Separately there's no direct write path for the newer OpenTelemetry-formatted traces in the v2 storage layer yet, so I need a new writer there that accepts OTel trace batches, converts them into the Cassandra internal data model, and writes each span one at a time, collecting all the per-span errors and returning them together instead of bailing on the first failure.

Also I need a little utility in the database model package that pulls span kind out of a span's tag list, since the new v2 write path needs that for operation name indexing.

And the tag filter config logic (blacklist vs whitelist vs drop-all) should live in the v2 factory. It needs to error when both a blacklist and a whitelist are configured at the same time, and when only one is set it just applies that one filter.

Why bother: killing the external library dependency on the write path shrinks the surface we have to touch every time the data model evolves and keeps the storage layer more self-contained.
