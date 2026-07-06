Refactor the telemetry system by implementing a class-based telemetry manager that buffers metrics and delivers them through a worker pool. Ensure the manager can be enabled or disabled via configuration and handles metric submission and flushing efficiently.

* Implement the Telemetry class in `ironfish/src/telemetry/telemetry.ts` with the following:
    * Constructor accepting a config object, worker pool, logger, and default tags.
    * `submit(metric: Metric): Promise<void>` method:
        * Return immediately if telemetry is disabled (config's enabled flag is false).
        * Reject with an error if the metric's fields array is empty.
        * Add valid metrics to an internal queue, increasing its length by one.
        * Automatically call `flush()` if the queue reaches `MAX_QUEUE_SIZE`.
    * `flush(): Promise<void>` method:
        * Call the pool's `submitTelemetry` method with the queued points.
        * Retain points in the queue and log an error if `submitTelemetry` throws an error and the queue is not full.
    * `start()` and `stop()` methods to manage telemetry operation.
    * Internal properties:
        * `points: Metric[]` — the internal metric queue.
        * `MAX_QUEUE_SIZE: number` — maximum queue size before automatic flush.
        * `pool: WorkerPool` — worker pool for submitting telemetry.
        * `logger: Logger` — logger for debug/error messages.

* Define the Metric interface in `ironfish/src/telemetry/interfaces/metric.ts`:
    * Fields:
        * `measurement: 'node'` — fixed literal string.
        * `name: string` — metric name.
        * `fields: Field[]` — must have at least one entry.
        * Optional `timestamp?: Date` and `tags?: Tag[]`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.