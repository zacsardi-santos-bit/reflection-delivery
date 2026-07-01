## Description

When a block is executed, the execution result data does not currently expose the write sets produced by each transaction. Write sets represent the exact state mutations committed to the chain during block execution. Without them being captured in the execution result, callers who need to inspect what state changed during a block have no way to do so without re-executing the block.

## Expected Behavior

- The block execution result data structure should include a field that collects the write sets from all committed transactions in the block.
- When no transactions have been executed (or when a default/empty execution result is constructed), the write sets collection should be empty (length zero).

## Why This Matters

Downstream consumers of block execution results — such as chain replay logic or block import pipelines — sometimes need access to the raw state changes produced by a block. Currently they cannot get this data from the execution result alone. Adding write set tracking to the execution result enables these use cases without requiring a second pass over the data.
