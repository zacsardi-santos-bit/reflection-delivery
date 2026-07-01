Implement a buffered, non-streaming order-by query execution mode in the CosmosDB JavaScript SDK to support queries that require global sorting, such as vector similarity ranking. Develop utility data structures and components to efficiently buffer, sort, and deduplicate results before returning them to the caller.

*   Implement the `NonStreamingOrderByMap` class in `sdk/cosmosdb/cosmos/src/utils/nonStreamingOrderByMap.ts`.
    *   Accept a comparator function in the constructor.
    *   Implement `set(key: string, value: T)`, `get(key: string): T | undefined`, `size(): number`, and `getAllValuesAndReset(): T[]` methods.
    *   Ensure `set()` updates values only if the comparator returns a positive number when comparing the new value to the existing value.
    *   Ensure `getAllValuesAndReset()` returns all stored values and clears the map.

*   Implement the `FixedSizePriorityQueue` class in `sdk/cosmosdb/cosmos/src/utils/fixedSizePriorityQueue.ts`.
    *   Accept a comparator function and a maximum size in the constructor.
    *   Implement `enqueue(item: T)`, `dequeue(): T | undefined`, `peek(): T | undefined`, `size(): number`, `isEmpty(): boolean`, and `getTopElements(): T[]` methods.
    *   Ensure `enqueue()` evicts the top-of-heap element when the queue exceeds its maximum size.
    *   Ensure `getTopElements()` drains all elements and returns them in reverse dequeue order, leaving the queue empty.

*   Implement the `supportedQueryFeaturesBuilder` function in `sdk/cosmosdb/cosmos/src/utils/supportedQueryFeaturesBuilder.ts`.
    *   Accept an optional boolean parameter `disableNonStreamingOrderByQuery`.
    *   Include "NonStreamingOrderBy" in the returned string unless the parameter is true.

*   Update the `FeedOptions` interface in `sdk/cosmosdb/cosmos/src/request/FeedOptions.ts`.
    *   Add an optional boolean property `disableNonStreamingOrderByQuery`.

*   Implement the `NonStreamingOrderByEndpointComponent` class in `sdk/cosmosdb/cosmos/src/queryExecutionContext/EndpointComponent/NonStreamingOrderByEndpointComponent.ts`.
    *   Accept an `ExecutionContext`, `sortOrders` array, and `bufferSize` number in the constructor.
    *   Store `sortOrders` and `bufferSize` as private fields `sortOrders` and `priorityQueueBufferSize`.
    *   Implement `hasMoreResults()` and `nextItem(diagnosticNode: any): Promise<{ result: any; headers: any }>` methods.
    *   Ensure `nextItem()` returns empty results while accumulating and sorted results afterward.
    *   Ensure the internal priority queue `nonStreamingOrderByPQ` has `size() === 0` after all results are consumed.

*   Implement the `NonStreamingOrderByDistinctEndpointComponent` class in `sdk/cosmosdb/cosmos/src/queryExecutionContext/EndpointComponent/NonStreamingOrderByDistinctEndpointComponent.ts`.
    *   Accept an `ExecutionContext`, `QueryInfo` object, and `bufferSize` number in the constructor.
    *   Set the private `sortOrders` field from `queryInfo.orderBy` and `priorityQueueBufferSize` from `bufferSize`.
    *   Implement `hasMoreResults()` and `nextItem(diagnosticNode: any): Promise<{ result: any; headers: any }>` methods.
    *   Ensure `nextItem()` returns empty results while accumulating and sorted results afterward.
    *   Ensure `finalResultArray` has length 0 after all results are consumed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.