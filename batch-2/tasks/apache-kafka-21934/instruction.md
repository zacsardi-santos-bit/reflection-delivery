I'm working on the Kafka Streams internals and I've noticed that the session window store materializer always builds a headers-capable store hierarchy, even when the underlying store supplier doesn't actually support headers.

*   The SessionStoreMaterializer class must detect at build time whether the provided store supplier also implements the HeadersBytesStoreSupplier interface. If it does, the builder must use the session-store-with-headers builder path, resulting in a MeteredSessionStoreWithHeaders as the outermost wrapper. If it does not, the builder must use the plain session store builder path, resulting in a store that is NOT an instance of SessionStoreWithHeaders.

*   When SessionStoreMaterializer builds a store with a HeadersBytesStoreSupplier and caching is enabled (and not auto-disabled): the wrapped store hierarchy must be MeteredSessionStoreWithHeaders -> CachingSessionStore -> ChangeLoggingSessionBytesStore (not ChangeLoggingSessionBytesStoreWithHeaders).

*   When SessionStoreMaterializer builds a store with a HeadersBytesStoreSupplier and caching is explicitly disabled (withCachingDisabled()) but logging is enabled: the wrapped hierarchy must be MeteredSessionStoreWithHeaders -> ChangeLoggingSessionBytesStoreWithHeaders.

*   When SessionStoreMaterializer builds a store with a HeadersBytesStoreSupplier and logging is disabled but caching is enabled: the wrapped hierarchy must be MeteredSessionStoreWithHeaders -> CachingSessionStore, and the CachingSessionStore's wrapped store must NOT be a ChangeLoggingSessionBytesStoreWithHeaders.

*   When SessionStoreMaterializer builds a store with a HeadersBytesStoreSupplier and both caching and logging are disabled: the hierarchy must be MeteredSessionStoreWithHeaders -> inner store (NOT CachingSessionStore, NOT ChangeLoggingSessionBytesStoreWithHeaders).

*   When SessionStoreMaterializer is configured with EmitStrategy.onWindowClose(), caching must be auto-disabled. If the supplier is a HeadersBytesStoreSupplier and caching was explicitly disabled via withCachingDisabled(): hierarchy must be MeteredSessionStoreWithHeaders -> ChangeLoggingSessionBytesStoreWithHeaders.

*   When SessionStoreMaterializer is configured with EmitStrategy.onWindowClose() and caching is auto-disabled (not explicitly disabled, i.e., default Materialized): if the supplier is a HeadersBytesStoreSupplier, the hierarchy must be MeteredSessionStoreWithHeaders -> ChangeLoggingSessionBytesStore (NOT ChangeLoggingSessionBytesStoreWithHeaders).

*   When SessionStoreMaterializer builds a store without a HeadersBytesStoreSupplier (using a regular Materialized.as(storeName)), the resulting store must NOT be an instance of SessionStoreWithHeaders regardless of caching or logging settings. The hierarchy must follow standard session store layering: CachingSessionStore -> ChangeLoggingSessionBytesStore when both are enabled.

*   A new class RocksDbTimeOrderedSessionHeadersBytesStoreSupplier must exist in the org.apache.kafka.streams.state.internals package. It must implement both SessionBytesStoreSupplier and HeadersBytesStoreSupplier. Its constructor must accept exactly three parameters: (String name, long retentionPeriod, boolean withIndex).

*   The class RocksDbTimeOrderedSessionBytesStoreSupplier must no longer accept a fourth boolean withHeaders parameter. Its constructor must accept exactly three parameters: (String name, long retentionPeriod, boolean withIndex). The four-parameter constructor variant must not exist.

*   When creating a session store for a store type that requires headers with index (RocksDBTimeOrderedSessionStoreWithHeadersWithIndex), the test infrastructure must use RocksDbTimeOrderedSessionHeadersBytesStoreSupplier(name, retentionPeriod, true). When requiring headers without index (RocksDBTimeOrderedSessionStoreWithHeadersWithoutIndex), it must use RocksDbTimeOrderedSessionHeadersBytesStoreSupplier(name, retentionPeriod, false).

*   Upgrade tests that open an existing time-ordered session store without headers and then reopen it with a RocksDbTimeOrderedSessionHeadersBytesStoreSupplier must successfully migrate the data and allow reads after the migration.


*   Interface details: Type: Class
Name: RocksDbTimeOrderedSessionHeadersBytesStoreSupplier
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/RocksDbTimeOrderedSessionHeadersBytesStoreSupplier.java
Description: A new supplier class for time-ordered session stores with headers support. Implements both SessionBytesStoreSupplier and HeadersBytesStoreSupplier (from org.apache.kafka.streams.state). Must NOT extend RocksDbTimeOrderedSessionBytesStoreSupplier.
Signature: RocksDbTimeOrderedSessionHeadersBytesStoreSupplier(String name, long retentionPeriod, boolean withIndex)

Type: Class
Name: RocksDbTimeOrderedSessionBytesStoreSupplier
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/RocksDbTimeOrderedSessionBytesStoreSupplier.java
Description: Existing class modified to remove the withHeaders boolean parameter. The only valid constructor must be the 3-parameter form. The 4-parameter constructor RocksDbTimeOrderedSessionBytesStoreSupplier(String name, long retentionPeriod, boolean withIndex, boolean withHeaders) must be removed.
Signature: RocksDbTimeOrderedSessionBytesStoreSupplier(String name, long retentionPeriod, boolean withIndex)

Type: Class
Name: SessionStoreMaterializer
Location: streams/src/main/java/org/apache/kafka/streams/kstream/internals/SessionStoreMaterializer.java
Description: Existing materializer class for DSL session window stores. The builder() method must be updated to return StoreBuilder<?> (previously StoreBuilder<SessionStoreWithHeaders<K, V>>). It must detect whether the resolved SessionBytesStoreSupplier also implements HeadersBytesStoreSupplier. If yes, delegate to Stores.sessionStoreBuilderWithHeaders(); if no, delegate to Stores.sessionStoreBuilder(). The constructor signature is unchanged.
Signature: SessionStoreMaterializer(MaterializedInternal<K, V, SessionStore<Bytes, byte[]>> materialized, SessionWindows sessionWindows, EmitStrategy emitStrategy)
Method: configure(StreamsConfig streamsConfig) -> void
Method: builder() -> StoreBuilder<?>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.