Implement metrics to track the connection status between the Jaeger agent and the collector. Add a gauge metric to reflect the current connection state and a counter metric to track the total number of successful connections. Update the gRPC connection method to accept a metrics factory for initializing these metrics.

*   Define a `ConnectMetrics` struct in `cmd/agent/app/reporter/connect_metrics.go`:
    *   Include a public field `MetricsFactory` of type `metrics.Factory`.
    *   Include a private internal metrics struct containing a counter and a gauge.
    *   Implement `NewConnectMetrics()` to initialize internal metrics under the "connection_status" namespace using the `MetricsFactory`.
*   Implement `OnConnectionStatusChange(connected bool)` method in `ConnectMetrics`:
    *   Set the gauge metric `connection_status.collector_connected` to 0 when `connected` is `false`.
    *   Set the gauge metric `connection_status.collector_connected` to 1 and increment the counter metric `connection_status.collector_reconnects` by 1 when `connected` is `true`.
    *   Ensure subsequent calls with `connected` as `true` continue to increment the counter.
*   Update `CreateConnection` function in `cmd/agent/app/reporter/grpc/builder.go`:
    *   Modify the function signature to `CreateConnection(logger *zap.Logger, mFactory metrics.Factory) (*grpc.ClientConn, error)`.
    *   Ensure the function accepts a `metrics.Factory` as the second parameter for metric initialization.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.