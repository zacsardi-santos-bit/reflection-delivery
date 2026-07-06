Implement a monitoring component for the supervisor service to track chain head state changes on connected L2 chains. Ensure the component subscribes to real-time unsafe head events and polls for safe and finalized head changes, notifying registered listeners accordingly. Incorporate lifecycle controls to start and stop monitoring cleanly.

*   Implement the `HeadMonitor` struct in `op-supervisor/supervisor/backend/source/heads.go`:
    *   Use `NewHeadMonitor(logger log.Logger, epochPollInterval time.Duration, rpc HeadMonitorClient, callback HeadChangeCallback) *HeadMonitor` to create a new `HeadMonitor` instance.
    *   Ensure monitoring only begins when `Start()` is called on the `HeadMonitor` instance.

*   Implement the `Start()` method for `HeadMonitor`:
    *   Subscribe to new unsafe head events using `SubscribeNewHead(ctx context.Context, unsafeCh chan<- *types.Header) (ethereum.Subscription, error)` from the `HeadMonitorClient`.
    *   Begin polling for safe and finalized head changes using the provided epoch poll interval.
    *   Return `nil` on successful execution.

*   Implement the `Stop()` method for `HeadMonitor`:
    *   Unsubscribe from all active subscriptions and stop all polling activities.
    *   Return `nil` on successful execution.

*   Handle unsafe head events:
    *   On receiving a new unsafe block header, invoke `OnNewUnsafeHead(ctx context.Context, block eth.L1BlockRef)` on the `HeadChangeCallback` with the corresponding `eth.L1BlockRef`.
    *   If the unsafe head subscription errors out, automatically resubscribe and continue forwarding notifications.

*   Implement periodic polling for head changes:
    *   Poll for safe head changes using `L1BlockRefByLabel(ctx context.Context, label eth.BlockLabel)` with the `eth.Safe` label. Notify via `OnNewSafeHead(ctx context.Context, block eth.L1BlockRef)` when changes occur.
    *   Poll for finalized head changes using `L1BlockRefByLabel(ctx context.Context, label eth.BlockLabel)` with the `eth.Finalized` label. Notify via `OnNewFinalizedHead(ctx context.Context, block eth.L1BlockRef)` when changes occur.

*   Ensure the `HeadMonitorClient` interface is implemented by any type providing:
    *   `SubscribeNewHead(ctx context.Context, unsafeCh chan<- *types.Header) (ethereum.Subscription, error)`
    *   `L1BlockRefByLabel(ctx context.Context, label eth.BlockLabel) (eth.L1BlockRef, error)`

*   Ensure the `HeadChangeCallback` interface is implemented by any type providing:
    *   `OnNewUnsafeHead(ctx context.Context, block eth.L1BlockRef)`
    *   `OnNewSafeHead(ctx context.Context, block eth.L1BlockRef)`
    *   `OnNewFinalizedHead(ctx context.Context, block eth.L1BlockRef)`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.