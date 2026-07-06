Implement a durable, disk-backed queue for the Prometheus remote write component in Grafana Alloy. Ensure that the queue persists data to disk, supports metadata, and handles restarts and errors gracefully. Implement the necessary types and methods to support this functionality.

*   Implement the `NewQueue` function in `internal/component/prometheus/remote/queue/filequeue/filequeue.go`:
    *   Accepts a directory path, an output callback of type `func(ctx context.Context, dh types.DataHandle)`, and a logger.
    *   Returns a `types.FileStorage` and nil error on success, creating the directory if it does not exist.
    *   Discovers any pre-existing `*.committed` files, sorts them by numeric prefix, and queues them for replay.

*   Ensure files written by the queue follow the naming pattern `<n>.committed`:
    *   Only files matching this pattern are treated as queue entries.
    *   Ignore unrelated files in the directory.

*   Implement the `Store` method in the `FileStorage` interface:
    *   When called with nil metadata and a byte payload, the `DataHandle` must have a `Pop` function returning a map of length 0, the original byte payload, and nil error.
    *   When called with non-nil metadata, `Pop` must return the same key-value pairs alongside the original payload and nil error.

*   Handle file corruption and deletion:
    *   If a queue file is corrupted or unparseable, `Pop` must return a non-nil error.
    *   If a queue file is deleted before `Pop` is called, `Pop` must return a non-nil error.
    *   Ensure subsequent valid entries are delivered normally.

*   Implement the `Record` struct in `internal/component/prometheus/remote/queue/filequeue/record.go`:
    *   Fields: `Meta map[string]string` and `Data []byte`.
    *   Implement the full msgp serialization interface: `MarshalMsg`, `UnmarshalMsg`, `EncodeMsg`, `DecodeMsg`, and `Msgsize`.
    *   Ensure `Msgsize` returns an upper-bound byte estimate.

*   Ensure the queue does not leak goroutines:
    *   All goroutines started by `Start` must be fully terminated after `Stop` returns.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.