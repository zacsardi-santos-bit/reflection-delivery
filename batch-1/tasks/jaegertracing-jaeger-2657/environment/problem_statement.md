## Description

The Jaeger agent currently has no way to expose its connection status with the collector via metrics. When the agent connects or disconnects from the collector, there is no observable signal, making it difficult to monitor the health of the agent-collector relationship in production environments.

## Expected Behavior

- There should be a gauge metric that reflects the current connection state between the agent and collector: a value of 1 means the agent is connected, and 0 means it is disconnected.
- There should be a counter metric tracking the total number of successful connections (including reconnects) to the collector.
- The method used to establish the gRPC connection to the collector should accept a metrics factory as an argument so that connection metrics can be properly scoped and initialized during connection setup.

## Why This Matters

Without connection status metrics, operators have no visibility into whether the agent is successfully communicating with the collector. Adding these metrics makes it easy to set up dashboards and alerts that detect connectivity issues between the agent and the collector in real time.
