## Description

When a script executed by the virtual machine fails because it was passed arguments that are too large, this is a deterministic failure: the transaction will always fail in exactly the same way, no matter what the blockchain state is. A transaction in this state is permanently invalid — it can never be made valid without changing the transaction itself.

Currently, this specific failure mode is not classified as a "malformed transaction." This is incorrect behavior. As a result, nodes do not properly penalize peers who relay permanently invalid transactions of this type, and they may attempt to re-accept them in the future.

## Expected Behavior

- When a script fails due to oversized arguments, the transaction that contains the script should be classified as malformed.
- The malformed classification should apply consistently to all script-level verification failures, since script failures are inherently tied to the transaction's own data.
- Existing logic that exempts this error type from the malformed transaction classification should be removed.

## Why This Matters

Correctly identifying malformed transactions allows the network to penalize peers who propagate invalid transactions and prevents wasted effort re-validating transactions that can never succeed. The previous special-casing of the argv-too-long error was inconsistent with how other deterministic script failures are handled.
