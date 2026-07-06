Implement a two-tier timeout system for the P2P networking layer to handle network requests more effectively. Create a stream wrapper that dynamically manages deadlines and ensure that timeout errors include diagnostic information. Additionally, update the peer discovery subsystem and fetch configuration with new timing options.

*   Create a new deadline-adjusting stream wrapper in `p2p/server`:
    *   Implement the `newDeadlineAdjuster` function in `p2p/server/deadline_adjuster.go` with the signature:
        ```go
        newDeadlineAdjuster(s peerStream, timeout time.Duration, hardTimeout time.Duration) *deadlineAdjuster
        ```
    *   Ensure the `deadlineAdjuster` struct wraps a `peerStream` and manages deadlines using a byte-threshold algorithm.
    *   Implement `Read` and `Write` methods in `deadlineAdjuster` that wrap the underlying stream's I/O, managing deadlines dynamically.
    *   Adjust deadlines using a byte-threshold mechanism, resetting thresholds after each adjustment by `chunkSize`.
    *   Set deadlines to `min(currentTime + timeout, hardDeadline)`, truncated to second granularity.
    *   Return `(0, ErrTimeout)` for `Read` and `Write` if `currentTime` exceeds `hardDeadline`.
    *   Wrap timeout errors with context including bytes transferred and timeout values.

*   Implement the `peerStream` interface in `p2p/server/interface.go`:
    *   Include methods: `Read([]byte) (int, error)`, `Write([]byte) (int, error)`, `Close() error`, `SetDeadline(time.Time) error`.
    *   Provide a typed mock `MockpeerStream` in `p2p/server/mocks`.

*   Update server and discovery configurations:
    *   Implement `WithHardTimeout` function in `p2p/server/server.go` to set the hard timeout for requests:
        ```go
        WithHardTimeout(timeout time.Duration) Opt
        ```
    *   Add timing configuration options in `p2p/dhtdiscovery/discovery.go`:
        *   `WithFindPeersRetryDelay(d time.Duration) Opt`
        *   `WithAdvertiseDelay(d time.Duration) Opt`
        *   `WithAdvertiseInterval(d time.Duration) Opt`
        *   `WithAdvertiseRetryDelay(d time.Duration) Opt`

*   Update the fetch configuration:
    *   Add `RequestHardTimeout` field to `fetch.Config` in `fetch/fetch.go`:
        ```go
        RequestHardTimeout time.Duration
        ```

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.