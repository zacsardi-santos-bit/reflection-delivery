Implement a new overload of the `initialize` method in the `Rmm` class to allow specifying a maximum pool size for the GPU memory manager. Ensure that the initialization fails with a descriptive error if the configuration is invalid.

*   Overload the `initialize` method in `java/src/main/java/ai/rapids/cudf/Rmm.java` to include a `maxPoolSize` parameter:
    *   Method signature: `initialize(int allocationMode, boolean enableLogging, long poolSize, long maxPoolSize) -> void`
*   Implement validation logic for the new `maxPoolSize` parameter:
    *   If `maxPoolSize` is greater than 0 and `allocationMode` is not `RmmAllocationMode.POOL`, throw `IllegalArgumentException`.
    *   If `maxPoolSize` is greater than 0 and `maxPoolSize` is less than `poolSize`, throw `IllegalArgumentException`.
    *   If `maxPoolSize` is 0 or less, allow the pool to grow without an upper limit, maintaining existing behavior.
*   Ensure error messages are clear and descriptive when throwing `IllegalArgumentException`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.