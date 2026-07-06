## Description

We need a new integration module that connects to the Chronicle Backstory security service using a streaming API, rather than the existing polling-based approach. The existing integration fetches alerts periodically, which introduces latency. The new integration should open a long-lived connection and receive detection alerts as they arrive in real time.

## Expected Behavior

- The integration must validate its configuration before use: service account credentials must be valid JSON, and the first-fetch time window must not exceed 7 days in the past.
- When credentials are invalid or the lookback window is out of range, the integration must raise a clear, specific error — not a generic crash.
- The integration must expose a function that checks connectivity by initiating a short streaming session and returning a success indicator or an informative error string.
- When the streaming connection drops or returns an error batch, the integration must describe the failure clearly (e.g., including the error content from the server).
- The integration must support retrying the stream connection automatically, backing off exponentially on consecutive failures, and raising a descriptive error after exceeding the maximum allowed consecutive failures.
- When a stale continuation time causes a 400 error from the server, the integration must surface the failure with a message that identifies the cause as invalid arguments along with the HTTP status and error body.
- Sample detection events must be persistable in the integration context and retrievable as a list.
- API errors must be translated into human-readable messages: rate-limit errors, permission denials, invalid region configurations, internal server errors, and non-JSON responses must each produce distinct, descriptive messages.

## Why This Matters

The polling-based approach misses the low-latency advantage of the streaming API. Security teams need detection alerts as close to real time as possible. Proper configuration validation and error handling are essential so that operators can quickly identify and resolve integration failures without having to dig through raw API responses.
