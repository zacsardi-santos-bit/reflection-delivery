Implement the necessary changes to ensure the Flink Kafka consumer properly closes its internal partition discoverer component in all shutdown scenarios, preventing resource leaks. Update the lifecycle management within existing classes to handle both error and normal cancellation cases.

*   Update `FlinkKafkaConsumerBase` in `flink-connectors/flink-connector-kafka-base/src/main/java/org/apache/flink/streaming/connectors/kafka/FlinkKafkaConsumerBase.java`:
    *   Modify the `close()` method to always close the partition discoverer, regardless of how the consumer stopped.
    *   Ensure that if the fetcher is running, the partition discoverer is woken up before being closed.

*   Modify `AbstractPartitionDiscoverer` in `flink-connectors/flink-connector-kafka-base/src/main/java/org/apache/flink/streaming/connectors/kafka/internals/AbstractPartitionDiscoverer.java`:
    *   Ensure `close()` and `wakeup()` lifecycle methods are exposed, delegating to `closeConnections()` and `wakeupConnections()` methods.

*   Update `MockStreamingRuntimeContext` in `flink-streaming-java/src/test/java/org/apache/flink/streaming/util/MockStreamingRuntimeContext.java`:
    *   Ensure the inner `MockStreamOperator` class overrides `getProcessingTimeService()` to return a lazily-initialized `TestProcessingTimeService` instance.

*   Ensure the following behaviors:
    *   When partition discovery throws an exception, the partition discoverer is closed when `close()` is called, and the original exception is propagated.
    *   When creating the Kafka fetcher throws an exception, the partition discoverer is closed when `close()` is called, and the original exception is propagated.
    *   When the Kafka fetcher's run loop throws an exception, wake up the partition discoverer before closing it.
    *   During normal cancellation, ensure the partition discoverer is properly closed.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.