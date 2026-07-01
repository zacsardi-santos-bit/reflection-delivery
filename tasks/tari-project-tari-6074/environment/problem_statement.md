## Description

The wallet's output manager currently treats any newly added output as immediately available for spending. This is incorrect behavior — outputs should not be spendable until they have been confirmed on-chain. As a result, there is no lifecycle distinction between an output that has just been added and one that has actually been mined and confirmed.

Additionally, there is a bug in balance calculation for hash time-locked contract sends: the value being sent is incorrectly deducted from the pending incoming balance, causing the reported balance to be lower than it should be.

## Expected Behavior

- When an output is added to the wallet, it should be stored in an unconfirmed/pending state and should not be available for spending until explicitly confirmed
- There must be a way to explicitly mark an output as confirmed and spendable (i.e., as a true unspent output) by referencing its hash
- This confirmation step should be available directly on both the backend storage type and the database wrapper, so it can be called independently from the service layer
- The balance reported after sending a hash time-locked contract transaction should reflect only the fee deduction from pending incoming balance — the value being sent should not be deducted from that balance

## Why This Matters

The current approach leads to outputs being treated as spendable before they are confirmed, which can cause incorrect balance reporting and allow spending of unconfirmed outputs. Fixing this ensures the wallet accurately tracks the output lifecycle and reports balances correctly.
