Implement a deduplication trigger node for a stream processing pipeline that processes time-range events. Ensure the node tracks processed intervals, emits only new portions of incoming intervals, and prunes old intervals based on a configurable expiration window. Maintain order by processing events based on their interval end time.

Requirements:

* Implement the `doTrigger` function in `internal/topo/node/dedup_trigger_op.go`:
    * Maintain a persistent histogram of processed intervals under the state key 'histogram', initialized as an empty slice of [][]int64.
    * Compute an expiration threshold as `now - exp`. Return nil if the incoming interval's end is less than this threshold. Clip the start to this threshold if necessary.
    * Prune histogram intervals with an end value less than the expiration threshold before processing new intervals.
    * Return a slice of maps representing uncovered sub-intervals, with keys 'start_key' and 'end_key' as string representations of int64 boundaries. Return nil if the interval is fully covered.
    * Update the histogram by merging new intervals into the existing sorted, non-overlapping intervals list.

* Define the `PriorityQueue` type in `internal/topo/node/dedup_trigger_op.go`:
    * Implement as a slice of `*TriggerRequest`, ordered by the `end` field.
    * Provide methods: `Push(*TriggerRequest)`, `Pop() *TriggerRequest`, and `Peek() *TriggerRequest`.

* Define the `TriggerRequest` struct in `internal/topo/node/dedup_trigger_op.go`:
    * Include at least `start` and `end` int64 fields, accessible within the package.

* Implement `NewDedupTriggerNode` in `internal/topo/node/dedup_trigger_op.go`:
    * Accept parameters: `name`, `options`, `aliasName`, `startField`, `endField`, `nowField`, `expire`.
    * Return a `*DedupTriggerNode` with an 'outputs' map, 'input' channel, and 'statManager'.

* Implement `DedupTriggerNode.Exec`:
    * Read input tuples from the node's input channel.
    * Use a `PriorityQueue` to process tuples in ascending end-time order.
    * Call `doTrigger` for each dequeued item and emit results as `xsql.Row` objects to `outputs["output"]`.
    * Ensure each emitted row includes all original input fields plus the `aliasName` field with the new interval ranges.

* Ensure the `DedupTriggerNode` processes items such that output order is determined by the `end` field value, holding items in the queue until their end time is reached or a later event triggers processing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.