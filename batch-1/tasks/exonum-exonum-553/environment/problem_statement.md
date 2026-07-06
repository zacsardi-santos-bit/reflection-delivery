# Transaction Execution Results Not Exposed via API

## Description

Currently, when a transfer transaction fails — for example, because the sender's wallet doesn't exist, the recipient's wallet doesn't exist, or the sender has insufficient funds — the blockchain simply ignores the failure and records the transaction as if it completed normally. There is no way for API consumers to determine whether a submitted transaction actually succeeded or failed after it was committed to a block.

This makes it impossible for clients to provide meaningful feedback to users about why a transfer did not take effect. A transaction that failed due to insufficient funds looks identical (from the API perspective) to one that succeeded.

## Expected Behavior

- After a block is created, clients should be able to query the blockchain explorer for a transaction's status and receive a clear indication of success or failure.
- Successful transactions should be reported with a success status.
- Failed transfer transactions should be reported with an error status that includes a numeric code identifying the specific reason for failure:
  - Sender wallet not found
  - Receiver wallet not found
  - Sender has insufficient balance

## Why This Matters

Without structured error reporting at the transaction level, users and downstream systems have no reliable way to confirm whether their operations completed successfully. Surfacing execution results through the explorer API makes it possible to build clients that react appropriately to each type of failure.
