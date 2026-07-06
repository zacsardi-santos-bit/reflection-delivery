Implement methods to address nonce inconsistencies between legacy and new-style accounts on the IoTeX blockchain. Ensure that legacy accounts, which have only received funds and never sent transactions, are correctly identified and converted to the new zero-nonce type when necessary.

*   Update the `Account` type in `state/account.go` with the following methods:
    *   `IsLegacyFreshAccount() bool`: 
        *   Return `true` if the account is of legacy nonce type (`accountType == 0`) and has a stored nonce of 0.
        *   Return `false` for all other accounts.
    *   `PendingNonceConsideringFreshAccount() uint64`:
        *   Return 0 if `IsLegacyFreshAccount()` is `true`.
        *   Return the same value as `PendingNonce()` for all other accounts.
    *   `ConvertFreshAccountToZeroNonceType(nonce uint64) bool`:
        *   Return `true` and set `accountType` to 1 if `IsLegacyFreshAccount()` is `true` and the supplied nonce is 0.
        *   Return `false` without modifying the account in all other cases.
        *   Ensure `PendingNonce()` and `PendingNonceConsideringFreshAccount()` return 0 after a successful conversion.
        *   Ensure `IsNewbieAccount()` returns `true` after conversion on a zero-nonce legacy fresh account.

*   Modify the action pool gas price validation path:
    *   Call the state reader's `Height()` method exactly twice during gas price requirement evaluation.
    *   Ensure the validation context includes a feature context and a block context with a non-zero `BlockHeight`.

*   Ensure block processing logic:
    *   Automatically convert legacy fresh accounts to zero-nonce account type when a block is committed at or beyond the protocol upgrade height.
    *   Maintain deterministic behavior across both in-memory and on-disk chain storage.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.