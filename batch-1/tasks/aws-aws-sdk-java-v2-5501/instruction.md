Implement a new variant of the transactional write operation in the DynamoDB Enhanced client that returns a response object with consumed capacity and item collection metrics. Extend both synchronous and asynchronous client variants to support this new operation, and update the request builder to specify the return of these metrics.

Requirements:

*   Create a new class `TransactWriteItemsEnhancedResponse` in the `software.amazon.awssdk.enhanced.dynamodb.model` package.
    *   Implement the builder pattern.
    *   Ensure `consumedCapacity()` returns a `List<ConsumedCapacity>`.
    *   Ensure `itemCollectionMetrics()` returns a `Map<String, List<ItemCollectionMetrics>>`.
    *   Implement `equals()` and `hashCode()` methods, treating `consumedCapacity` and `itemCollectionMetrics` as non-null for equality.

*   Extend `TransactWriteItemsEnhancedRequest` in the same package.
    *   Add builder methods `returnConsumedCapacity(ReturnConsumedCapacity)` and `returnItemCollectionMetrics(ReturnItemCollectionMetrics)`.
    *   Implement getters `returnConsumedCapacity()`, `returnConsumedCapacityAsString()`, and `returnItemCollectionMetrics()`.

*   Update the internal transactional write operation.
    *   Transform a low-level `TransactWriteItemsResponse` into a `TransactWriteItemsEnhancedResponse`.
    *   Map consumed capacity and item collection metrics from the low-level response to the enhanced response object.

*   Update the synchronous enhanced DynamoDB client.
    *   Add `transactWriteItemsWithResponse(TransactWriteItemsEnhancedRequest)` method returning `TransactWriteItemsEnhancedResponse`.
    *   Ensure the response contains consumed capacity and item collection metrics when requested.

*   Update the asynchronous enhanced DynamoDB client.
    *   Add `transactWriteItemsWithResponse(TransactWriteItemsEnhancedRequest)` method returning `CompletableFuture<TransactWriteItemsEnhancedResponse>`.
    *   Ensure the response contains consumed capacity and item collection metrics when requested.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.