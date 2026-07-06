Implement changes to the Teku beacon node to detect when the old ETH1-based deposit mechanism is no longer active and stop unnecessary polling activities. Ensure that the system reflects the current network state and avoids misleading logs and metrics updates.

*   Update `Eth1DataCache` class:
    *   Modify the constructor to accept `Spec` as the first parameter: `Eth1DataCache(Spec spec, MetricsSystem metricsSystem, Eth1VotingPeriod eth1VotingPeriod)`.
    *   Implement `getEth1Vote(BeaconState beaconState)` to return `beaconState.getEth1Data()` when `Spec.isFormerDepositMechanismDisabled(beaconState)` is true.
    *   Ensure `updateMetrics(BeaconState beaconState)` does not update vote-related metrics when the former deposit mechanism is disabled; all gauges should remain at 0.

*   Update `PowchainService` class:
    *   Modify the constructor to accept a `Supplier<Optional<BeaconState>>` for the latest finalized state: `PowchainService(ServiceConfig serviceConfig, PowConfig powConfig, Optional<ExecutionWeb3jClientProvider> maybeExecutionWeb3jClientProvider, Supplier<Optional<BeaconState>> latestFinalizedState)`.
    *   Implement an `initialize()` method to set up deposit snapshot resources, separate from the constructor.
    *   Ensure `start()` runs the service without initializing an ETH1 deposit manager when the former deposit mechanism is disabled, as determined by the latest finalized state.
    *   Ensure `getEth1DepositManager()` returns null when ETH1 polling is disabled.
    *   Implement `onNewFinalizedCheckpoint(Checkpoint checkpoint, boolean fromOptimisticBlock)` to stop the service when the former deposit mechanism is disabled.

*   Update `AsyncRunLoop` class:
    *   Ensure `stop()` prevents `advance()` from being called again after the scheduled delay has elapsed.

*   Implement `isFormerDepositMechanismDisabled(BeaconState beaconState)` in the `Spec` class to determine the status of the former deposit mechanism for a given state.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.