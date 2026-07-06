Implement a revised flow for handling new pending transactions in the wallet activity session feed. Ensure that session update events only signal the presence of new entries and allow clients to request a session reset to fetch the full updated activity list with clear new entry indications.

*   Update the `GenerateTestPendingTransactions` function in `transactions/testhelpers.go`:
    *   Accept two integer parameters: `start` and `count`.
    *   Generate transactions starting from the `start` offset up to (but not including) `count`.

*   Modify the `TestTxSummary` struct in `transactions/testhelpers.go`:
    *   Include exported fields: `DontConfirm` (bool) and `Timestamp` (int).
    *   Use `Timestamp` to override the generated transaction's timestamp if greater than 0.

*   Update the `SessionUpdate` struct in `services/wallet/activity/session.go`:
    *   Replace any previous `NewEntries []Entry` field with `HasNewEntries *bool`.
    *   Set `HasNewEntries` to a non-nil pointer to true when new entries are detected.

*   Modify the `Entry` struct in `services/wallet/activity/activity.go`:
    *   Add an `isNew` bool field.
    *   Ensure `isNew` is correctly marshalled/unmarshalled in JSON as 'isNew' (omitted when false).

*   Implement the `ResetFilterSession` method on `Service` in `services/wallet/activity/session.go`:
    *   Signature: `ResetFilterSession(id SessionID, firstPageCount int) error`.
    *   Trigger an asynchronous filter emitting `EventActivityFilteringDone` with a `FilterResponse`.
    *   Ensure `FilterResponse` has `ErrorCodeSuccess` and entries marked with `isNew` as true for new entries and false otherwise.

*   Implement the `findUpdates` function in `services/wallet/activity/session.go`:
    *   Signature: `findUpdates(identities []EntryIdentity, updated []Entry) (new []mixedIdentityResult, removed []EntryIdentity)`.
    *   Return new entries with their position index in `updated` and removed entries.

*   Define the `mixedIdentityResult` struct in `services/wallet/activity/session.go`:
    *   Fields: `newPos` (int) and `id` (EntryIdentity).

*   Ensure the `EventActivitySessionUpdated` notification carries a `SessionUpdate` payload with `HasNewEntries` set to non-nil and true when new entries are detected.

*   After `ResetFilterSession` is called, ensure the `FilterResponse` activity list matches the `firstPageCount` requested, with newly arrived entries marked `isNew=true` and pre-existing entries marked `isNew=false`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.