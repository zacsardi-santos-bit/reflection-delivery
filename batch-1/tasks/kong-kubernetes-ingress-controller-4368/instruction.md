Address the issues in the Kubernetes Ingress Controller's admin API client manager by refining endpoint discovery logic and enhancing client readiness management. Implement the following requirements to ensure correct handling of endpoint states and client readiness transitions.

*   Update endpoint discovery logic:
    *   Include endpoints with `Ready=false` and `Terminating=false` (or nil).
    *   Exclude endpoints with `Terminating=true`, regardless of their ready status.

*   Implement the `ReadinessChecker` interface:
    *   Define `CheckReadiness(ctx context.Context, alreadyCreatedClients []AlreadyCreatedClient, pendingClients []adminapi.DiscoveredAdminAPI) ReadinessCheckResult`.
    *   Ensure `ReadinessCheckResult` struct includes:
        *   `ClientsTurnedReady` of type `[]*adminapi.Client`.
        *   `ClientsTurnedPending` of type `[]adminapi.DiscoveredAdminAPI`.
        *   `HasChanges() bool` method to indicate non-empty fields.

*   Define the `AlreadyCreatedClient` interface:
    *   Methods: `IsReady(ctx context.Context) error`, `PodReference() (k8stypes.NamespacedName, bool)`, `BaseRootURL() string`.

*   Implement `NewDefaultReadinessChecker`:
    *   Accepts `ClientFactory` and `logr.Logger`.
    *   Calls `CreateAdminAPIClient` for each pending client.
    *   Calls `IsReady` on each already-created client.

*   Modify `AdminAPIClientsManager`:
    *   Replace `RunNotifyLoop()` with `Run()` method to start the processing loop.
    *   Ensure `Running()` channel remains open until `Run()` is called.
    *   On receiving notifications with discovered clients, call `ReadinessChecker.CheckReadiness`.
    *   Apply results from `CheckReadiness` to update active gateway clients and notify subscribers.

*   Implement `NewAdminAPIClientsManager`:
    *   Accepts `ReadinessChecker` and variadic `AdminAPIClientsManagerOption`.
    *   Returns an error with the message "at least one initial client must be provided" if `initialClients` is nil or empty.

*   Support readiness reconciliation:
    *   Use `WithReadinessReconciliationTicker(ticker Ticker)` to override the default ticker.
    *   Ensure periodic readiness reconciliation occurs every `DefaultReadinessReconciliationInterval` (10 * time.Second).

*   Create a mock `Ticker` in the `test/mocks` package:
    *   Implement `Stop()`, `Channel() <-chan time.Time`, `Reset(d time.Duration)`, and a method to trigger tick events manually.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.