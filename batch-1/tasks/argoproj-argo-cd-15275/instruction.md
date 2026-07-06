Implement improvements to the Redis integration in Argo CD's caching layer to address reconnect detection and metrics collection issues. Ensure that DNS resolution errors trigger reconnects and that Redis operation metrics are accurately collected.

*   Update the reconnect detection mechanism:
    *   Modify the `NewArgoRedisHook` function in `util/cache/redis_hook.go` to use DNS error type inspection instead of error string matching.
    *   Ensure the reconnect callback is invoked during a DNS resolution error in Redis operations, such as a cache Set call to an unreachable host.
    *   Ensure the reconnect callback is not invoked when Redis operations succeed against a reachable host.
    *   Return a non-nil error for DNS resolution failures during a cache Set call.
    *   Return a nil error when a cache Set call succeeds against a reachable Redis host.

*   Implement the metrics collection functionality:
    *   Create the `CollectMetrics` function in `util/cache/redis.go` with the following signature:
        *   `CollectMetrics(client *redis.Client, registry MetricsRegistry)`
    *   Attach a metrics-collecting hook to the given Redis client to track subsequent operations.
    *   Ensure the metrics hook calls `IncRedisRequest(false)` for each successful Redis command.
    *   Ensure the metrics hook calls `IncRedisRequest(true)` for each failed Redis command.
    *   Ensure the metrics hook calls `ObserveRedisRequestDuration` with the elapsed duration for every Redis command, regardless of success or failure.

*   Ensure the `MetricsRegistry` interface in `util/cache/redis.go` is correctly implemented to receive Redis request telemetry:
    *   Methods to be implemented:
        *   `IncRedisRequest(failed bool)`
        *   `ObserveRedisRequestDuration(duration time.Duration)`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.