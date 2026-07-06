## Description

Cilium's Kubernetes client connections can become silently broken when the API server becomes unresponsive. Currently, if the API server stops replying, existing connections hang indefinitely with no automatic recovery mechanism. We need a heartbeat function that periodically probes API server reachability and resets connections when the server is unreachable.

## Expected Behavior

- If a successful API server response was received very recently (within a configurable time window), no heartbeat probe should be sent — the connection is presumed healthy.
- If no successful response has been received recently, a probe should be sent with a bounded timeout.
- If the probe times out (the server does not respond within the allowed window), all existing client connections should be forcefully closed so that new ones can be established.
- If the probe returns an error from the API server, all existing client connections should be forcefully closed.
- If the probe succeeds within the timeout, connections should remain open.

## Supporting Infrastructure

To support this, the metrics package that already tracks the timestamp of the last API interaction needs an additional metric that specifically tracks the timestamp of the last *successful* API interaction (a response indicating success, such as 2xx or 4xx status codes). This distinction is important: a connection may still be alive even if recent attempts have returned errors, but if it has never returned a success response in the heartbeat window, a fresh probe is warranted.

## Why This Matters

Without this mechanism, a stuck or broken connection to the Kubernetes API server can leave Cilium unable to receive policy updates or node events, with no automatic way to recover. The heartbeat provides a safety net that detects and resolves these situations automatically.
