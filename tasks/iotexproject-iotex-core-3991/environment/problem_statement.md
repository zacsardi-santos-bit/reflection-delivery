## Description

There is a nonce inconsistency between old-style accounts and new-style accounts on the IoTeX blockchain. Accounts created under the legacy protocol scheme that have only ever received funds — but have never sent a transaction — report a pending nonce of 1 rather than 0. This contradicts the newer account type, where a truly fresh account starts at nonce 0.

This discrepancy is problematic because: (1) it makes it harder to detect whether a "fresh" account belongs to the legacy or the new scheme, and (2) once a protocol upgrade activates, these legacy fresh accounts need to be able to submit their first transaction using nonce 0 (consistent with the new scheme) rather than nonce 1.

## Expected Behavior

- It should be possible to programmatically identify legacy accounts that have never sent an outgoing transaction ("legacy fresh" accounts).
- There should be a way to retrieve the nonce for such an account that is consistent with the zero-nonce account interpretation (i.e., reports 0 instead of 1).
- There should be a mechanism to convert a legacy fresh account to the zero-nonce account type, after which the account behaves identically to a brand-new account with nonce 0.
- The conversion should happen automatically during block processing once the relevant protocol upgrade height is reached.
- The conversion must be applied consistently whether chain state is held in memory or persisted to disk.

## Why This Matters

Without this fix, legacy fresh accounts cannot participate correctly in the upgraded nonce scheme, leading to rejected transactions and inconsistent state between nodes. The upgrade path for these accounts needs to be smooth and automatic so that users of such accounts are unaffected.
