## Description

There is currently no controlled, in-process way to test code that depends on an agent session. Writing tests for features that send messages to an agent, observe streaming events, manage session state, or handle interrupted streams requires either a live backend connection or ad-hoc fakes that have to be rebuilt per-project. This makes testing unreliable and tedious.

## Expected Behavior

- A reusable mock agent session implementation should be available in the core package.
- Developers should be able to pre-load the mock with a set of events that will be returned when a message is sent.
- The mock should automatically inject appropriate bookkeeping events (stream boundaries, user messages, session updates, elicitation responses) based on what was sent, so tests don't have to manually build full event sequences.
- The mock should support pausing and resuming streams so asynchronous behaviors — like waiting for new events or being aborted mid-stream — can be tested deterministically.
- The mock should expose the full history of events that flowed through the session so tests can assert on the complete event log.
- Sending an unsupported action type should raise a clear error describing what was attempted.

## Why This Matters

Without a proper mock, any code that consumes an agent session is difficult to unit-test. Providing a first-class testing utility in the core package makes it straightforward to write fast, deterministic tests for all agent-session-driven features without requiring network access or real agent infrastructure.
