I'm working on improving the resilience of Kafka Streams state stores backed by RocksDB.

*   RocksDBStore must expose a public static byte[] constant named OFFSETS_COLUMN_FAMILY_NAME that identifies the dedicated column family used to store lifecycle status and committed offsets.

*   RocksDBStore must create and maintain the OFFSETS_COLUMN_FAMILY_NAME column family in its underlying RocksDB database alongside the data column families; after a clean close, the 'status' key in this column family must hold the value 0L (serialized as a long).

*   RocksDBStore.commit(Map<TopicPartition, Long> changelogOffsets) must persist the provided offsets into the offsets column family, storing each TopicPartition's toString() representation as the key (UTF-8 bytes) and the offset as a serialized long value.

*   RocksDBStore.committedOffset(TopicPartition partition) must return the last offset that was committed for the given partition, retrieving it from the offsets column family.

*   When a RocksDBStore is opened with exactly-once semantics (EOS) enabled and the persisted status in the offsets column family indicates the store was previously left open (value 1L), the store must throw ProcessorStateException with the message 'State store <storeName> didn't find a valid state, since under EOS it has the risk of getting uncommitted data in stores'.

*   When a RocksDBStore is opened without exactly-once semantics and the persisted status indicates the store was previously left open, the store must NOT throw an exception; it must proceed normally and committed offsets must remain retrievable via committedOffset().

*   AbstractColumnFamilyAccessor.open(DBAccessor accessor, boolean ignoreInvalidState) must: when status is 0L (closed), write 1L to the 'status' key; when status is 1L (open) and ignoreInvalidState is false, throw ProcessorStateException with message 'Invalid state during store open. Expected state to be either empty or closed'; when status is 1L and ignoreInvalidState is true, set storeOpen to true and write 1L without throwing.

*   AbstractColumnFamilyAccessor.close(DBAccessor accessor) must write 0L to the 'status' key in the offsets column family.

*   AbstractColumnFamilyAccessor.commit(DBAccessor accessor, Map<TopicPartition, Long> changelogOffsets) must call flush on the DBAccessor with the relevant column family handles and must put each partition's toString() key mapped to its serialized long offset into the offsets column family.

*   DualColumnFamilyAccessor constructor must accept, in order: a ColumnFamilyHandle for the offsets column family, a ColumnFamilyHandle for the old column family, a ColumnFamilyHandle for the new column family, a Function<byte[], byte[]> valueConverter, a RocksDBStore instance, and an AtomicBoolean storeOpen.

*   DualColumnFamilyAccessor.close(DBAccessor accessor) must call the parent close (writing 0L to status) and then close both the old and new column family handles.

*   DualColumnFamilyAccessor.commit(DBAccessor accessor, ...) must flush all three column families: old, new, and offsets.

*   RocksDBGenericOptionsToDbOptionsColumnFamilyOptionsAdapter.setAtomicFlush(boolean) must NOT delegate to the underlying RocksDB option; instead it must log a WARN-level message: 'AtomicFlush is explicitly set to True by Streams in RocksDB. Setting this option to \'false\' will be ignored'.


*   Interface details: Type: Class
Name: AbstractColumnFamilyAccessor
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/AbstractColumnFamilyAccessor.java
Description: Abstract base class for RocksDB column family accessors that manages lifecycle status and committed offsets in a dedicated offsets column family. Implements RocksDBStore.ColumnFamilyAccessor.
Signature:
  AbstractColumnFamilyAccessor(ColumnFamilyHandle offsetColumnFamilyHandle, AtomicBoolean storeOpen)
  final void open(RocksDBStore.DBAccessor accessor, boolean ignoreInvalidState) throws RocksDBException
  void close(RocksDBStore.DBAccessor accessor) throws RocksDBException
  final void commit(RocksDBStore.DBAccessor accessor, Map<TopicPartition, Long> changelogOffsets) throws RocksDBException
  final Long getCommittedOffset(RocksDBStore.DBAccessor accessor, TopicPartition partition) throws RocksDBException
  protected abstract void flush(RocksDBStore.DBAccessor accessor, ColumnFamilyHandle offsetColumnFamilyHandle) throws RocksDBException

Type: Class
Name: DualColumnFamilyAccessor
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/DualColumnFamilyAccessor.java
Description: Concrete column family accessor for stores that migrate between old and new column family formats. Extends AbstractColumnFamilyAccessor.
Signature:
  DualColumnFamilyAccessor(ColumnFamilyHandle offsetColumnFamily, ColumnFamilyHandle oldColumnFamily, ColumnFamilyHandle newColumnFamily, Function<byte[], byte[]> valueConverter, RocksDBStore store, AtomicBoolean storeOpen)
  void close(RocksDBStore.DBAccessor accessor) throws RocksDBException
  void flush(RocksDBStore.DBAccessor accessor, ColumnFamilyHandle offsetColumnFamilyHandle) throws RocksDBException

Type: Class
Name: RocksDBStore
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/RocksDBStore.java
Description: RocksDB-backed Kafka Streams state store with persistent lifecycle tracking and offset management.
Signature:
  protected static final byte[] OFFSETS_COLUMN_FAMILY_NAME  (value: "offsets" encoded as UTF-8 bytes)
  void commit(Map<TopicPartition, Long> changelogOffsets)
  Long committedOffset(TopicPartition partition)

Type: Class
Name: RocksDBGenericOptionsToDbOptionsColumnFamilyOptionsAdapter
Location: streams/src/main/java/org/apache/kafka/streams/state/internals/RocksDBGenericOptionsToDbOptionsColumnFamilyOptionsAdapter.java
Description: Adapter class that wraps RocksDB DBOptions and ColumnFamilyOptions. The setAtomicFlush method must NOT delegate to the underlying RocksDB option; instead it must log a WARN-level message and return without modifying the option.
Signature:
  Options setAtomicFlush(boolean atomicFlush)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.