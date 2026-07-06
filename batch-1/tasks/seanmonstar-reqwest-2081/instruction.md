Resolve the deadlock issue in your HTTP/2 client when sending many concurrent requests to a server with strict stream limits and slow connection handshakes. Ensure that all requests eventually complete successfully without hanging indefinitely.

*   Implement logic in the HTTP/2 client to handle situations where the server limits the number of concurrent streams to one.
    *   Ensure that when 100 concurrent requests are sent, they are queued and dispatched sequentially as capacity becomes available.
    *   Ensure that all requests eventually receive a 200 OK response, even if they must wait in line.
*   Implement handling for scenarios where the server introduces a delay (e.g., 2 seconds) after a TCP connection is established before sending initial configuration frames.
    *   Ensure that the client does not deadlock or stall during this delay, and all requests eventually complete successfully.
*   Ensure compatibility of the client behavior on both multi-threaded and single-threaded async runtimes.
*   Update dependencies in `Cargo.toml`:
    *   Include `futures-util` as a dev-dependency with version 0.3.0 or later, enabling `std` and `alloc` features.
    *   Update the `h2` dependency to version 0.3.14 or later to ensure correct HTTP/2 stream error handling.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.