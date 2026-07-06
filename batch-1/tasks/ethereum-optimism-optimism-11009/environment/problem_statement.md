## Description

The supervisor service needs a way to track and respond to chain head changes on the L2 chains it monitors. Currently there is no component responsible for subscribing to new block arrivals or polling for safe and finalized head updates. Without this, the supervisor has no real-time awareness of chain progression.

## Expected Behavior

- A head monitoring component should be able to subscribe to real-time unsafe block notifications from a connected chain and forward those notifications to registered listeners.
- When the unsafe block subscription encounters an error or drops, the monitor should automatically recover by resubscribing, without requiring any manual intervention.
- The monitor should periodically poll the connected chain for safe and finalized head changes and notify registered listeners whenever these change.
- The monitoring component should have explicit lifecycle controls — it should only begin monitoring after being started, and it should cleanly stop all subscriptions and polls when stopped.

## Why This Matters

The supervisor needs up-to-date awareness of chain head state to perform interop consolidation and populate its databases correctly. Without a dedicated monitoring component that tracks unsafe, safe, and finalized heads separately and notifies the rest of the system, the supervisor cannot react to chain state changes. This component forms the foundation for all downstream processing that depends on knowing the current chain head.
