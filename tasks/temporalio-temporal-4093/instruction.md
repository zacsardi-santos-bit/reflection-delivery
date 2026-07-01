Implement a streaming-based cross-cluster replication infrastructure by creating a bidirectional streaming wrapper, a task progress tracker, and a stream receiver. Ensure these components work together to support efficient, low-latency replication.

*   Implement `BiDirectionStreamImpl` in `service/history/replication/bi_direction_stream.go`:
    *   Create via `NewBiDirectionStream[S, R any]` with a client provider, metrics handler, and logger.
    *   Initialize `streamingClient` to nil and `status` to `streamStatusInitialized` (0).
    *   Define constants `streamStatusInitialized`, `streamStatusOpen`, and `streamStatusClosed` with values 0, 1, and 2.
    *   Implement `lazyInit()` to initialize `streamingClient` on first call, idempotent on subsequent calls, and return `ErrClosed` after closure.
    *   Implement `Send(req S) error` to forward requests and update status on errors.
    *   Implement `Recv() (<-chan StreamResp[R], error)` to return a channel of responses, closing on EOF or error.
    *   Embed `sync.Mutex` for thread safety.

*   Implement `ExecutableTaskTrackerImpl` in `service/history/replication/executable_task_tracker.go`:
    *   Create via `NewExecutableTaskTracker(logger log.Logger)`.
    *   Maintain `taskQueue` as a `*list.List` and `highWatermarkInfo` as a `*WatermarkInfo`.
    *   Implement `TrackTasks(highWatermarkInfo WatermarkInfo, tasks ...TrackableExecutableTask)` to append tasks, deduplicate, and update watermark.
    *   Implement `LowWatermark() *WatermarkInfo` to compute and return the low-watermark.

*   Implement `StreamReceiver` in `service/history/replication/stream_receiver.go`:
    *   Create via `NewStreamReceiver(processToolBox ProcessToolBox, sourceShardKey ClusterShardKey, targetShardKey ClusterShardKey)`.
    *   Maintain unexported fields `taskTracker`, `sourceShardKey`, and `targetShardKey`.
    *   Implement `ackMessage(stream Stream)` to send acknowledgments based on the low-watermark.
    *   Implement `processMessages(stream Stream) error` to handle incoming messages, track tasks, and schedule them.

*   Define `StreamResp[R any]` in `bi_direction_stream.go` with fields `Resp` and `Err`.
*   Define `WatermarkInfo` in `executable_task_tracker.go` with fields `Watermark` and `Timestamp`.
*   Define `ClusterShardKey` in `stream_receiver.go` with fields `ClusterName` and `ShardID`.
*   Implement `ExecutableUnknownTask` in `replication/` to handle unrecognized task types.

*   Generate mocks `MockExecutableTaskTracker` and `MockTrackableExecutableTask` in `service/history/replication/executable_task_tracker_mock.go` using gomock.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.