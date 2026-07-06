I'm working on the CosmosDB JavaScript SDK and need to add support for a buffered, non-streaming order-by query execution mode. Some queries — particularly those involving vector similarity ranking — require the SDK to collect all results from the backend before it can sort and return them to the caller, because the final ordering isn't known until all documents have been seen.

To support this, I need a few new pieces:

First, a comparator-driven map that stores one value per key and updates that value only when the new value "wins" according to the comparator — this is useful for deduplication during accumulation.

Second, a fixed-size priority queue that evicts elements automatically when it reaches capacity, keeping only the top elements according to a comparator. It should support peeking at the current top, draining all elements in sorted order (destructively), and reporting its size and emptiness.

Third, a utility function that assembles the list of query features the client advertises to the server. It should include the non-streaming order-by capability by default, but accept a flag to suppress it — useful when connecting to older gateways that might reject unknown features.

Fourth, a new option in the query feed options type that lets callers disable the non-streaming order-by feature for compatibility with older environments.

Finally, two query execution pipeline components: one that buffers and sorts results using the priority queue and returns empty placeholder results while accumulating, then real results afterward; and a second variant that additionally deduplicates results using the map before returning them in sorted order.
