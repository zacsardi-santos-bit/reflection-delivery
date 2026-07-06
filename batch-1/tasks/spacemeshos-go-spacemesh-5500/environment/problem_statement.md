## Description

The P2P networking layer currently uses a single fixed timeout for all network requests, which is too blunt an instrument. A connection that is actively streaming data at a slow but steady rate should not be cut off the same way as one that is completely stalled. At the same time, there must be an absolute upper bound on how long any single request can take, regardless of activity.

We need a two-tier timeout system:
- A **per-chunk adaptive timeout** that resets as data flows, giving active connections room to breathe.
- A **hard (absolute) timeout** that serves as a ceiling no connection can exceed, even if it keeps sending or receiving data.

When a timeout does occur, the error should include diagnostic information — specifically how many bytes were read and written, and the values of both timeout parameters — so operators can understand what happened.

## Expected Behavior

- Active connections that transfer data continuously should not be dropped prematurely.
- No connection should exceed the hard timeout, regardless of how much data it transfers.
- Timeout errors should carry context: bytes transferred in each direction and both timeout durations.
- The fetch configuration should expose a field for setting this hard timeout.
- The peer discovery subsystem should gain new timing configuration options for controlling advertisement delay, advertisement interval, advertisement retry delay, and peer-finding retry delay — useful for both testing and production tuning.

## Why This Matters

Without a hard timeout cap, a pathological peer could keep a connection alive indefinitely by trickling data. Without the adaptive per-chunk timeout, a connection doing a large legitimate transfer over a slow link would be incorrectly terminated. The new system handles both cases correctly, and the diagnostic error messages make production debugging significantly easier.
