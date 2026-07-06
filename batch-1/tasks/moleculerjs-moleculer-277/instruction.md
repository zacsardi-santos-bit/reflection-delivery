Implement a threshold-based circuit breaker that opens based on failure rate and request count within a time window. Ensure the circuit breaker resets periodically and handles half-open states correctly. Update event payloads for better monitoring.

*   Export a new state constant:
    *   Add `CIRCUIT_HALF_OPEN_WAIT` with value `'half_open_wait'` in `src/constants.js`.

*   Update `ActionEndpointCB` class in `src/registry/endpoint-cb.js`:
    *   Initialize `reqCount` to 0 and `windowTimer` as an active interval timer in the constructor.
        *   `windowTimer` should reset `failures` and `reqCount` every `windowTime` seconds (default 60).
    *   Merge `opts` with defaults: `{ enabled: false, threshold: 0.5, windowTime: 60, minRequestCount: 20, halfOpenTime: 10000, failureOnTimeout: true, failureOnReject: true }`.
    *   Remove the `maxFailures` option.
    *   Modify the `isAvailable` getter to return false when the state is `CIRCUIT_HALF_OPEN_WAIT`.

*   Modify methods in `ActionEndpointCB`:
    *   `failure(err)`: Increment `reqCount` and check if the circuit should open based on `reqCount >= minRequestCount` and `(failures / reqCount) >= threshold`.
    *   `success()`: Increment `reqCount`. If the state is `CIRCUIT_HALF_OPEN_WAIT`, call `circuitClose()`. Otherwise, apply the threshold check.
    *   `circuitOpen()`: Emit `$circuit-breaker.opened` and `metrics.circuit-breaker.opened` events with payload: `{ nodeID: string, action: string, failures: number, reqCount: number, rate: number }`.
    *   `circuitHalfOpen()`: Transition to `CIRCUIT_HALF_OPEN` and emit `$circuit-breaker.half-opened` event with `{ nodeID: string, action: string }`.
    *   `circuitClose()`: Transition to `CIRCUIT_CLOSE`, reset `failures`, and set `reqCount` to 1 if it is the first request after reset.

*   Update default configuration in `src/service-broker.js` and `src/registry/registry.js`:
    *   Replace `maxFailures: 3` with `{ threshold: 0.5, windowTime: 60, minRequestCount: 20 }`.

*   Ensure integration tests use:
    *   `threshold: 0.5`, `minRequestCount: 5`, `windowTime: 30`.
    *   Circuit opens after 3 failures and 2 successes with payload `{ nodeID: 'slave-1', action: 'cb.angry', failures: 3, reqCount: 5, rate: 0.6 }`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.