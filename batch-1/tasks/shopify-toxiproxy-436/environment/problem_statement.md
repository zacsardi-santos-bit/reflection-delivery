## Description

When a toxic is removed from a proxy connection that is actively transmitting data, the system can deadlock. This happens because the removal process tries to forward buffered data to the next stage in the pipeline by writing directly to an output channel, but if nothing is consuming that channel at the moment, the write blocks forever — hanging the entire proxy.

## Expected Behavior

- Removing a toxic from a live, active connection should always complete in a bounded amount of time, even when the output channel is temporarily blocked.
- When attempting to forward buffered data during toxic removal, the system should use a timeout-based write so that it does not hang indefinitely.
- If the data cannot be forwarded within the timeout, the operation should fail gracefully with a descriptive error rather than blocking forever.

## Why This Matters

Toxiproxy is used to simulate network conditions in testing environments. When toxics (e.g., bandwidth limiters) are added and then removed while connections are active, the proxy must remain responsive. The current behavior — where removing a toxic can cause an indefinite deadlock — makes it impossible to safely clean up toxics from live connections, reducing the reliability of test setups that rely on dynamic toxic management.
