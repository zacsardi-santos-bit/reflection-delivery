Implement a datastore-backed storage system for CA journals in the SPIRE server's CA manager. Ensure that CA journals are stored both on disk and in the datastore, and provide mechanisms for managing, querying, and pruning these journals. Integrate context handling and telemetry metrics for all operations.

Requirements:

*   Update the DataStore interface in `pkg/server/datastore/datastore.go` to include:
    *   `SetCAJournal(ctx context.Context, caJournal *CAJournal) (*CAJournal, error)`
        *   Create or update a CA journal. Return gRPC InvalidArgument error if `caJournal` is nil. Return gRPC NotFound error if `caJournal.ID` is non-zero and does not exist.
    *   `FetchCAJournal(ctx context.Context, activeX509AuthorityID string) (*CAJournal, error)`
        *   Fetch a CA journal by `activeX509AuthorityID`. Return gRPC InvalidArgument error if ID is empty.
    *   `PruneCAJournals(ctx context.Context, allCAsExpireBefore int64) error`
        *   Delete journals where all entries have expired before `allCAsExpireBefore`.
    *   `ListCAJournalsForTesting(ctx context.Context) ([]*CAJournal, error)`
        *   List all CA journals for testing.

*   Define the `CAJournal` struct in `pkg/server/datastore/datastore.go` or `pkg/server/datastore/types.go` with fields:
    *   `ID` (uint)
    *   `ActiveX509AuthorityID` (string)
    *   `Data` ([]byte)

*   Implement the `LoadJournal` function in `pkg/server/ca/manager/journal.go`:
    *   Accept `context.Context` and `*journalConfig`.
    *   Handle errors for invalid PEM and protobuf data.

*   Modify the `Journal` struct in `pkg/server/ca/manager/journal.go`:
    *   Include unexported fields `config` and `entries`.
    *   Implement methods `getEntries()`, `setEntries(entries *journal.Entries)`, and `save(ctx context.Context) error`.

*   Update the `AppendX509CA`, `AppendJWTKey`, `UpdateX509CAStatus`, and `UpdateJWTKeyStatus` methods in `pkg/server/ca/manager/journal.go` to accept `context.Context`.

*   Add a `PruneCAJournals(ctx context.Context) error` method to the CA manager in `pkg/server/ca/manager/manager.go`.

*   Update the `X509CAEntry` proto message in `proto/private/server/journal/journal.proto` to include a `NotAfter` field.

*   Implement telemetry metrics in `pkg/common/telemetry/server/datastore/wrapper.go` for the new DataStore methods.

*   Ensure that the CA rotator in `pkg/server/ca/rotator/rotator.go` calls `PruneCAJournals` at the interval defined by `pruneCAJournalsInterval`.

*   Rename and introduce constants:
    *   `safetyThresholdBundle` for bundle pruning.
    *   `safetyThresholdCAJournals` for journal pruning.
    *   `pruneBundleInterval` and `pruneCAJournalsInterval` for respective pruning operations.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.