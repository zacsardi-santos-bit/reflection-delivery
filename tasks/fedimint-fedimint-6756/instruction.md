Add a new database key prefix to the wallet client module to track whether the connected federation supports processing all Bitcoin deposits, including large transactions. Update the database migration test to include this new prefix, ensuring it compiles and passes.

*   Implement a new variant in the `DbKeyPrefix` enum:
    *   Add `SupportsSafeDeposit` as a variant in the `DbKeyPrefix` enum located in `modules/fedimint-wallet-client/src/client_db.rs`.
    *   Ensure `SupportsSafeDeposit` is added alongside existing variants like `PegInTweakIndex`, `ClaimedPegIn`, `RecoveryFinalized`, `RecoveryState`, etc., before any reserved range variants.
*   Ensure compatibility with the database migration test:
    *   Verify that the `test_client_db_migrations` function in `modules/fedimint-wallet-tests/tests/tests.rs` references `client_db::DbKeyPrefix::SupportsSafeDeposit` in its exhaustive match.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.