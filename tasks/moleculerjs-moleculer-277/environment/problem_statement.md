## Description

The current circuit breaker implementation uses a simple failure count to decide when to open the circuit. Once a fixed number of failures is reached, the circuit trips — regardless of how many total requests were made. This approach is too coarse: a single burst of failures in an otherwise healthy endpoint can unnecessarily open the circuit, while a sustained high failure rate on a heavily-loaded endpoint may not trip it at the right time.

We need to switch to a threshold-based approach: the circuit should only open when the failure **rate** (failures divided by total requests) exceeds a configurable percentage, and only after a minimum number of requests have been observed in the current time window. The time window should reset periodically so that old failure data doesn't permanently affect the circuit.

## Expected Behavior

- The circuit breaker configuration should accept a failure rate threshold (a percentage, e.g. 0.5 for 50%) and a minimum request count instead of a raw failure count.
- The circuit should not open until at least the configured minimum number of requests have been processed within the current window.
- Once the minimum request count is reached, the circuit opens if the failure rate meets or exceeds the configured threshold.
- A time window (in seconds) periodically resets both the failure count and the request count.
- A new intermediate state is introduced for the moment between the half-open probe request being dispatched and its result being received. In this state, the endpoint must be treated as unavailable to additional requests.
- When the circuit opens, the event it emits should include not only the failure count but also the total request count and the computed failure rate, making the event useful for monitoring and alerting.
- When the circuit half-opens, its event should also use stable identifiers (node ID as a string, action name as a string) rather than internal object references.

## Why This Matters

The threshold-based approach makes the circuit breaker significantly more reliable across different traffic volumes. It prevents false trips in low-traffic periods and ensures the circuit faithfully reflects the actual health of an endpoint under real load. Richer event payloads make it easier to build dashboards and alerts around circuit state changes.
