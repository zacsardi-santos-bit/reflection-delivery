Implement a heartbeat function in Cilium to manage Kubernetes API server connections, ensuring automatic detection and recovery from unresponsive states. Update the metrics package to track the timestamp of the last successful API server interaction.

*   Implement the `runHeartbeat` function in `pkg/k8s/client.go` with the following signature:
    *   `runHeartbeat(heartBeat func(context.Context) error, timeout time.Duration, closeAllConns ...func())`
    *   Accept a heartbeat callback, a timeout duration, and one or more close-connections callbacks.
*   Ensure `runHeartbeat` behaves as follows:
    *   If `LastSuccessInteraction` was updated within the timeout duration before `runHeartbeat` is called, return immediately without invoking any callbacks.
    *   If `LastSuccessInteraction` is older than the timeout duration, invoke the heartbeat callback with a context that expires after the timeout duration.
    *   If the heartbeat callback does not return before the context deadline, invoke all provided close-connections callbacks.
    *   If the heartbeat callback returns a nil error before the context deadline, do not invoke any close-connections callbacks.
    *   If the heartbeat callback returns a non-nil error, including a Kubernetes `StatusError` with HTTP status code 408, invoke all provided close-connections callbacks.
*   Update the `pkg/k8s/metrics/metrics.go` to export a `LastSuccessInteraction` variable:
    *   `LastSuccessInteraction` should be of type `eventTimestamper`, similar to the existing `LastInteraction` variable.
    *   Implement a `Reset()` method to record the current time as the interaction timestamp.
    *   Implement a `Time()` method to return the stored timestamp.
    *   Use `LastSuccessInteraction` in `runHeartbeat` to determine if a heartbeat probe is needed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.