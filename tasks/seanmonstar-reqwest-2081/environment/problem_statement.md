## Description

The HTTP/2 client can hang indefinitely when making many concurrent requests to a server that limits the number of simultaneous streams to a low value, especially when the server is also slow to complete the initial connection setup.

## Steps to Reproduce

1. Create a client configured to use HTTP/2 directly (without upgrade negotiation)
2. Start an HTTP/2 server configured to allow only 1 concurrent stream per connection
3. Have the server introduce a delay of a few seconds between accepting a TCP connection and sending its initial configuration frames (simulating a slow or overloaded server)
4. Send ~100 concurrent requests to this server from the client

## Expected Behavior

All 100 requests should eventually complete successfully with a 200 OK response. Requests that cannot be served immediately due to stream limits should queue up and be dispatched as capacity becomes available.

## Actual Behavior

The client hangs indefinitely. The requests never complete. The program must be killed manually.

## Why This Matters

Real-world HTTP/2 servers often enforce stream concurrency limits, and network conditions can cause slow connection handshakes. The client should handle this gracefully — queueing and dispatching requests as capacity frees up — rather than deadlocking. This is particularly important when the async runtime has only a single thread, as the blocking wait for connection setup can prevent the event loop from making progress on queued requests.
