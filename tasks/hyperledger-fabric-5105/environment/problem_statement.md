## Description

The chaincode stub interface used throughout Hyperledger Fabric's system chaincodes currently does not support write batching. There is no way to group multiple state write operations into a single batch or to commit them together as a unit. This is a gap in the interface when compared to the underlying infrastructure, which supports batched writes for efficiency.

## Expected Behavior

- The chaincode stub interface should expose a method to begin a write batch
- The interface should expose a method to finish (commit) a write batch, which can signal an error if the commit fails
- The mock implementations of the chaincode stub used in testing should reflect these new interface methods, including the ability to track how many times each method was called

## Why This Matters

Without write batch support in the interface and its test mocks, developers cannot write chaincode that groups state writes for efficiency, and tests cannot verify that write batch methods are invoked correctly. Updating both the interface and its mocks ensures that system chaincodes can take advantage of batched writes and that tests accurately reflect the expected behavior.
