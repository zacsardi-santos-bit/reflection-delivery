Implement the "Highest Bid" query as part of the NexMark streaming benchmark suite. Extend the existing SQL query implementations by creating a new class to process bid events, grouping them into tumbling time windows, and returning only those bids with the highest price in each window.

*   Create a class named `SqlQuery7` in the package `org.apache.beam.sdk.nexmark.queries.sql`.
    *   Extend `PTransform<PCollection<Event>, PCollection<Row>>`.
    *   Implement the constructor `SqlQuery7(NexmarkConfiguration configuration)` to accept a `NexmarkConfiguration` object and use its `windowSizeSec` field to set the tumbling window duration in seconds.
*   Implement the method `expand(PCollection<Event> allEvents)` to:
    *   Filter the input `PCollection<Event>` to process only bid events.
    *   Group bid events into fixed-duration tumbling time windows based on the configured window size.
    *   Calculate the maximum bid price within each window.
    *   Return a `PCollection<Row>` containing bids whose price equals the maximum price observed in their respective windows.
        *   Include all bids in the output if they tie for the highest price within the same window.
        *   Exclude bids with prices below the maximum price in their window.
*   Ensure each output `Row` contains exactly three fields:
    *   'auction' (long/BigInt)
    *   'price' (long/BigInt)
    *   'bidder' (long/BigInt)
*   Integrate `SqlQuery7` into the existing NexMark benchmark launcher to enable execution as part of the full benchmark suite.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.