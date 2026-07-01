Implement a system to manage wallet outputs such that they are only spendable once confirmed on-chain. Fix the balance calculation for hash time-locked contract (HTLC) transactions to ensure accurate reporting.

*   Modify output addition:
    *   Ensure outputs added via `add_output` or `add_unspent_output` are stored with `OutputStatus::UnspentMinedUnconfirmed`.
    *   Outputs should not be immediately spendable upon addition.

*   Implement output confirmation:
    *   Create a method `mark_output_as_unspent` in the `OutputManagerBackend` trait and its implementations to transition outputs from `UnspentMinedUnconfirmed` to `Unspent`.
    *   The method signature is `mark_output_as_unspent(&self, hash: FixedHash) -> Result<(), OutputManagerStorageError>`.
    *   Ensure this method is accessible on both the backend storage type and the database wrapper.

*   Update balance calculation for HTLC transactions:
    *   Ensure the `pending_incoming_balance` reflects only the deduction of transaction fees after an HTLC send.
    *   Include both the change output and the HTLC output value in the `pending_incoming_balance`.

*   Enhance database functionality:
    *   Implement `Clone` for `OutputManagerSqliteDatabase` to allow retaining a reference after passing it to the output manager service.
    *   Add an `oms_db` field of type `OutputManagerDatabase<OutputManagerSqliteDatabase>` to `TransactionServiceNoCommsInterface`.

*   Adjust test infrastructure:
    *   Modify the `setup_transaction_service` test helper to return a 6-element tuple, with the last element being the `OutputManagerSqliteDatabase` instance.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.