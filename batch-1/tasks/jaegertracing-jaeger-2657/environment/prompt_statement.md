I'm poking at the Jaeger agent's gRPC connection to the collector and there's a real gap in observability. Right now nothing in the metrics tells me whether the agent is actually talking to the collector or how flaky that link has been, so if connectivity drops in prod I've got no signal to alert on. I want to fix that.

Two metrics to add. First, a gauge that reflects the current connection state, 1 when the agent is connected to the collector and 0 when it's disconnected, so I can graph the live state. Second, a counter that ticks up on every successful connect, and that includes reconnects, so I can see churn over time and catch a collector that keeps flapping.

For this to be scoped correctly, the method that builds the gRPC connection to the collector needs to also take a metrics factory as an argument, so the connection-status metrics get initialized with the right scoping right there during connection setup. Track both under a connection status namespace, with a distinct metric name for the connected-status gauge and another for the reconnects counter.

The point is operators get dashboards and alerts on the agent-collector relationship in real time, which today just isn't possible. Wire the gauge to flip on connect and off on disconnect, and bump the counter each time a connection or reconnection succeeds.
