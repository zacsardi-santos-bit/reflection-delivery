## Description

The inbound proxy's policy system distinguishes between a "server address" type and the "original destination address" type, even though these represent the same thing in the inbound context. This distinction creates unnecessary complexity and coupling in the policy enforcement path, requiring multiple conversions and making the code harder to reason about. Additionally, the integration test harness always spins up a policy controller service even for tests that have no need for dynamic policy discovery, which adds overhead and brittleness to the test suite.

## Expected Behavior

- The inbound policy system should use the original destination address type consistently throughout — including in the authorization permit, connection metadata, and policy lookup interfaces — eliminating the unnecessary "server address" wrapper.
- The policy lookup interface should be synchronous rather than returning a future, since policy state is already held in memory.
- The test proxy builder should allow tests to disable protocol detection on specific inbound ports without needing to configure and run a full policy controller service.
- When an inbound server is unavailable (e.g., connection times out), the proxy should return a "bad gateway" response to the client rather than hanging or crashing.

## Why This Matters

The current design forces every component that works with inbound policies to track two separate address type concepts that mean the same thing. Simplifying to a single address type reduces conversion noise and clarifies the data flow. Removing the mandatory policy controller from integration tests makes those tests faster, simpler to set up, and less likely to fail for reasons unrelated to the behavior being tested.
