I'm working on adding headers-aware window state store support to Kafka Streams.

*   The RocksDbIndexedTimeOrderedWindowBytesStoreSupplier static create() method must accept a 6th boolean parameter withHeaders. When withHeaders=false, it returns the existing RocksDBTimeOrderedWindowStore behavior unchanged. When withHeaders=true (with withIndex=true), it must return a RocksDBTimeOrderedWindowStoreWithHeaders instance wrapping a RocksDBTimeOrderedWindowSegmentedBytesStore that has hasIndex() returning true.

*   The RocksDbIndexedTimeOrderedWindowBytesStoreSupplier constructor must accept a 7th boolean parameter withHeaders (after the existing withIndex parameter). Existing validation behavior is unchanged: NullPointerException for null name (message: 'name cannot be null'), IllegalArgumentException for negative retentionPeriod (message: 'retentionPeriod cannot be negative'), negative windowSize (message: 'windowSize cannot be negative'), or windowSize larger than retentionPeriod (message format: 'The retention period of the window store <name> must be no smaller than its window size. Got size=[<size>], retention=[<retention>]').

*   The WindowStoreMaterializer's builder().build() must return a MeteredTimestampedWindowStore wrapping a CachingWindowStore wrapping a ChangeLoggingTimestampedWindowBytesStore by default (when caching and logging are both enabled, DSL_STORE_FORMAT_CONFIG = 'timestamped').

*   When caching is disabled, WindowStoreMaterializer's builder().build() must return a MeteredTimestampedWindowStore where the first wrapped layer is a ChangeLoggingTimestampedWindowBytesStore (no CachingWindowStore).

*   When logging is disabled, WindowStoreMaterializer's builder().build() must return a MeteredTimestampedWindowStore wrapping a CachingWindowStore, where the inner wrapped layer of the CachingWindowStore is NOT a ChangeLoggingTimestampedWindowBytesStore.

*   When both caching and logging are disabled, WindowStoreMaterializer's builder().build() must return a MeteredTimestampedWindowStore where the first wrapped layer is neither a CachingWindowStore nor a ChangeLoggingTimestampedWindowBytesStore.

*   When a custom WindowBytesStoreSupplier is provided via Materialized.as(supplier), the built store's name() must match the name of the inner store provided by the supplier.

*   When EmitStrategy.onWindowClose() is used with caching enabled, WindowStoreMaterializer's builder().build() must use TimeOrderedCachingWindowStore as the caching layer instead of CachingWindowStore.

*   When DSL_STORE_FORMAT_CONFIG is set to 'headers' with caching disabled, WindowStoreMaterializer's builder().build() must return a MeteredTimestampedWindowStoreWithHeaders wrapping a ChangeLoggingTimestampedWindowBytesStoreWithHeaders.

*   When DSL_STORE_FORMAT_CONFIG is 'headers' and logging is disabled, WindowStoreMaterializer's builder().build() must return a TimestampedWindowStoreWithHeaders with a CachingWindowStore as the first wrapped layer, and the inner wrapped layer must NOT be a ChangeLoggingTimestampedWindowBytesStore.

*   When DSL_STORE_FORMAT_CONFIG is 'headers' with caching enabled (default), WindowStoreMaterializer's builder().build() must return a TimestampedWindowStoreWithHeaders whose first wrapped layer is a CachingWindowStore.

*   When DSL_STORE_FORMAT_CONFIG is 'headers' and both caching and logging are disabled, WindowStoreMaterializer's builder().build() must return a TimestampedWindowStoreWithHeaders where the wrapped layer is neither a CachingWindowStore nor a ChangeLoggingTimestampedWindowBytesStoreWithHeaders.

*   When DSL_STORE_FORMAT_CONFIG is 'headers' and EmitStrategy.onWindowClose() is used with caching enabled, WindowStoreMaterializer's builder().build() must return a TimestampedWindowStoreWithHeaders whose first wrapped layer is a TimeOrderedCachingWindowStore.

*   SlidingWindowStoreMaterializer must have the same store layering behavior as WindowStoreMaterializer across all caching/logging/headers combinations, except that EmitStrategy.onWindowClose() must NOT change the caching layer to TimeOrderedCachingWindowStore — it must always use CachingWindowStore when caching is enabled.

*   The classes CachingWindowStore, ChangeLoggingTimestampedWindowBytesStore, ChangeLoggingTimestampedWindowBytesStoreWithHeaders, MeteredTimestampedWindowStore, MeteredTimestampedWindowStoreWithHeaders, and TimeOrderedCachingWindowStore must be declared public (not package-private) so they can be imported and used from tests in other packages.


*   Interface details: ## Modified Classes (Already Exist, Need Changes)

---

Type: Class
Name: WindowStoreMaterializer
Location: streams/src/main/java/org/apache/kafka/streams/kstream/internals/WindowStoreMaterializer.java
Description: EXISTING class. Modify the builder() method to support headers-aware store construction based on the DSL store format configuration. When DSL_STORE_FORMAT_CONFIG is "headers", use timestampedWindowStoreWithHeadersBuilder instead of timestampedWindowStoreBuilder.
Signature:
  WindowStoreMaterializer(MaterializedInternal<K, V, WindowStore<Bytes, byte[]>> materialized, Windows<?> windows, EmitStrategy emitStrategy)
  void configure(StreamsConfig config)
  StoreBuilder<?> builder()

---

Type: Class
Name: SlidingWindowStoreMaterializer
Location: streams/src/main/java/org/apache/kafka/streams/kstream/internals/SlidingWindowStoreMaterializer.java
Description: EXISTING class. Modify the builder() method to support headers-aware store construction based on the DSL store format configuration. Same headers logic as WindowStoreMaterializer, but EmitStrategy.onWindowClose() must NOT switch to TimeOrderedCachingWindowStore.
Signature:
  SlidingWindowStoreMaterializer(MaterializedInternal<K, V, WindowStore<Bytes, byte[]>> materialized, SlidingWindows windows, EmitStrategy emitStrategy)
  void configure(StreamsConfig config)
  StoreBuilder<?> builder()

---

Type: Class
Name: RocksDbIndexedTimeOrderedWindowBytesStoreSupplier
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/RocksDbIndexedTimeOrderedWindowBytesStoreSupplier.java
Description: EXISTING class. Add a boolean withHeaders parameter to both the static create() method and the constructor. When withHeaders=true (with withIndex=true), the get() method returns a RocksDBTimeOrderedWindowStoreWithHeaders. Also add INDEXED_WINDOW_STORE_WITH_HEADERS to the WindowStoreTypes enum.
Signature:
  static RocksDbIndexedTimeOrderedWindowBytesStoreSupplier create(String name, Duration retentionPeriod, Duration windowSize, boolean retainDuplicates, boolean withIndex, boolean withHeaders)
  RocksDbIndexedTimeOrderedWindowBytesStoreSupplier(String name, long retentionPeriod, long defaultSegmentInterval, long windowSize, boolean retainDuplicates, boolean withIndex, boolean withHeaders)
  WindowStore<Bytes, byte[]> get()

---

## New Classes (Need to Be Created)

Type: Class
Name: RocksDBTimeOrderedWindowStoreWithHeaders
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/RocksDBTimeOrderedWindowStoreWithHeaders.java
Description: NEW class. A headers-aware variant of RocksDBTimeOrderedWindowStore. Created by RocksDbIndexedTimeOrderedWindowBytesStoreSupplier when withHeaders=true. Wraps a RocksDBTimeOrderedWindowSegmentedBytesStore with index enabled (hasIndex()=true).

---

## Visibility Changes Required (Currently Package-Private, Must Be Made Public)

The following existing classes must have their access modifier changed from package-private to public. The tests import these classes from a different package and use them in instanceof checks, which requires public visibility.

Type: Class
Name: CachingWindowStore
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/CachingWindowStore.java
Description: Change class declaration from package-private to public.

Type: Class
Name: ChangeLoggingTimestampedWindowBytesStore
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/ChangeLoggingTimestampedWindowBytesStore.java
Description: Change class declaration from package-private to public.

Type: Class
Name: ChangeLoggingTimestampedWindowBytesStoreWithHeaders
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/ChangeLoggingTimestampedWindowBytesStoreWithHeaders.java
Description: Change class declaration from package-private to public.

Type: Class
Name: MeteredTimestampedWindowStore
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/MeteredTimestampedWindowStore.java
Description: Change class declaration from package-private to public.

Type: Class
Name: MeteredTimestampedWindowStoreWithHeaders
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/MeteredTimestampedWindowStoreWithHeaders.java
Description: Change class declaration from package-private to public.

Type: Class
Name: TimeOrderedCachingWindowStore
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/TimeOrderedCachingWindowStore.java
Description: Change class declaration from package-private to public.

---

## Pre-existing Classes Referenced by Tests (Already Public or in Same Package)

The following classes are referenced by the tests and must exist. They exist already in the codebase and should not need to be created:

- org.apache.kafka.streams.state.TimestampedWindowStoreWithHeaders (interface/class)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.