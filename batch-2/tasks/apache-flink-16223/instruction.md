Implement a configurable delivery timeout for the RabbitMQ source connector in Flink to prevent indefinite blocking and allow graceful job cancellation. Ensure the timeout is configurable in milliseconds or with an explicit time unit, with a default of 30 seconds if not set. Reject negative timeout values with an error.

* Update `RMQConnectionConfig.Builder`:
    * Implement `setDeliveryTimeout(long deliveryTimeout)`:
        * Accepts a timeout in milliseconds.
        * Stores the timeout and returns the Builder for method chaining.
        * Throws `IllegalArgumentException` if `deliveryTimeout` is negative.
    * Implement `setDeliveryTimeout(long deliveryTimeout, TimeUnit unit)`:
        * Converts the given duration to milliseconds.
        * Delegates to the single-argument `setDeliveryTimeout`.
        * Throws `IllegalArgumentException` if the resulting timeout is negative.
* Update `RMQConnectionConfig`:
    * Implement `getDeliveryTimeout()`:
        * Returns the configured delivery timeout in milliseconds.
        * Returns 30000 (30 seconds) if no timeout is explicitly set.
* Update `RMQSource`:
    * Use `consumer.nextDelivery(long timeout)` with the timeout from `RMQConnectionConfig.getDeliveryTimeout()`.
    * Ensure that when `cancel()` is called, the source exits cleanly without throwing exceptions, even if no messages have been delivered.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.