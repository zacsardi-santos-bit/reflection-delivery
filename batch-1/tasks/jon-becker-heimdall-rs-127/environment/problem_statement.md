## Description

The EVM emulator currently lacks the ability to compute accurate gas costs for memory expansion and storage access operations. Without these computations, gas accounting during EVM simulation is incomplete and does not match how Ethereum actually charges for these operations.

Specifically:

- There is no way to calculate how much gas it costs to expand memory to accommodate a given region (based on its starting offset and size). Memory cost in Ethereum grows quadratically and an implementation must accurately reflect this.
- There is no way to compute the storage access cost for a given slot that distinguishes between a "cold" access (first time a slot is touched, which is expensive) and a "warm" access (slot has already been read or written in the same transaction, which is cheap). This distinction was introduced in Ethereum as part of access-list pricing improvements.
- There is no way to compute the full cost of writing to a storage slot, which depends on both whether the value being written is zero or non-zero, and whether the slot is warm or cold.

## Expected Behavior

- Memory must provide a method to query its current gas cost based on the number of 32-byte words currently allocated.
- Memory must provide a method to compute the incremental gas cost of expanding to a new region, returning zero if no expansion is needed.
- Storage must track which slots have been accessed (via read or write) within a session.
- Storage must provide a method to return the access cost for a given slot: a high cost for first-time ("cold") access and a low cost for repeat ("warm") access.
- Storage must provide a method to compute the total write cost for a slot+value pair, combining the non-zero/zero write base cost with the warm/cold access cost.

## Why This Matters

Accurate gas cost computation is essential for any tool that analyzes or simulates Ethereum smart contracts. Without it, gas estimates are wrong, and higher-level features that depend on gas accounting (such as function-level gas profiling) cannot work correctly.
