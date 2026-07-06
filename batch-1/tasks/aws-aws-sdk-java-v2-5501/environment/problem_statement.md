## Description

When using the DynamoDB Enhanced client to perform transactional write operations, developers currently have no way to retrieve metadata about the operation — specifically consumed capacity information and item collection metrics. The existing transactional write method returns nothing, so even if you tell DynamoDB to report consumed capacity or collection metrics, you have no way to access that data.

This is a problem for teams that need to monitor throughput consumption or track item collection sizes as part of cost management, capacity planning, or operational visibility.

## Expected Behavior

- The enhanced client should offer a variant of the transactional write operation that returns a response object containing the results of the write.
- The response object should include consumed capacity data (a list of per-table consumed capacity entries) and item collection metrics (a map from table name to a list of metrics).
- When requesting capacity reporting or collection metrics on the transactional write, those values should be populated in the returned response.
- Both the synchronous and asynchronous enhanced client variants should support this new operation.
- The request builder should allow specifying whether consumed capacity and item collection metrics should be returned by DynamoDB.

## Why This Matters

Without this feature, developers who want to understand the resource cost of their transactional writes must fall back to the lower-level DynamoDB client, losing the convenience and type-safety of the enhanced client. Adding a response-returning variant of the transactional write method fills this gap and makes the enhanced client consistent with the expressiveness expected for production use.
