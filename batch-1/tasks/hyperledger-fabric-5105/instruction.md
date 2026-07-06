Update the ChaincodeStub mock in the `core/scc/cscc/mocks/chaincode_stub.go` file to support write batching by implementing new methods. Ensure these methods track invocation counts to maintain compatibility with the updated chaincode library.

*   Implement the `StartWriteBatch()` method in the ChaincodeStub mock.
    *   This method should take no parameters and return no value.
*   Implement the `FinishWriteBatch()` method in the ChaincodeStub mock.
    *   This method should take no parameters and return an error.
    *   By default, return `nil` when no stub function is configured.
*   Implement the `StartWriteBatchCallCount()` method.
    *   This method should return an `int` representing the number of times `StartWriteBatch()` has been called.
*   Implement the `FinishWriteBatchCallCount()` method.
    *   This method should return an `int` representing the number of times `FinishWriteBatch()` has been called.
*   Ensure that after calling `StartWriteBatch()` exactly once, `StartWriteBatchCallCount()` returns 1.
*   Ensure that after calling `FinishWriteBatch()` exactly once, `FinishWriteBatchCallCount()` returns 1.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.