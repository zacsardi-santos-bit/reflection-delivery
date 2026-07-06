## Description

With the introduction of a newer Ethereum deposit mechanism in more recent network upgrades, the old ETH1-based deposit polling approach becomes unnecessary once the network transitions past a certain point. However, the beacon node currently has no awareness of this transition: it continues polling for ETH1 deposits, logging alerts about missing deposits, computing ETH1 votes, and updating metrics as though the old mechanism were still required, even when the network has clearly moved beyond it.

This creates unnecessary resource usage and potentially confusing operator logs about missing data that is no longer relevant.

## Expected Behavior

- When the current network state indicates that the former deposit mechanism has been disabled, the system should stop logging deposit-availability events — even when not all previously required deposits are present.
- When the former deposit mechanism is disabled, the ETH1 data vote returned for a state should reflect the state's own current ETH1 data rather than a computed cache-based vote.
- When the former deposit mechanism is disabled, vote-related metrics should not be updated (gauges should remain at zero).
- When the ETH1 polling service starts up and detects (via the latest finalized state) that the former deposit mechanism is disabled, it should start without initializing any ETH1 deposit manager.
- When a newly finalized checkpoint is processed and the former deposit mechanism is determined to be disabled, the ETH1 polling service should shut itself down automatically.
- The background processing loop used by this service must properly respect stop signals so that it does not continue advancing after being told to stop.

## Why This Matters

Without these changes, nodes running the latest protocol upgrades will continue performing unnecessary ETH1 work and logging misleading warnings. Operators will see confusing output suggesting deposit data is missing, even though the network no longer requires it. Automatically disabling ETH1 polling when the new deposit mechanism takes over keeps the node efficient, correct, and easier to operate.
