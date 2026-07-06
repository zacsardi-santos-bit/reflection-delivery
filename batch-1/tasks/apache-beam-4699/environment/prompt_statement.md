I'm working on the NexMark streaming benchmark and I need to implement Query 7, the "Highest Bid" query, which is currently missing from the SQL query implementations. The benchmark already has several other SQL queries implemented, and I need to add this one to fill the gap.

The query should take a stream of auction events, filter for bid events, group them into fixed-duration tumbling time windows, and for each window return only the bids whose price equals the highest price observed in that window. If multiple bids tie for the top price within the same window, all of them should be included in the output. The window size should come from the existing benchmark configuration. The output should carry the auction ID, price, and bidder for each qualifying bid.

Once implemented, this query should also be wired into the benchmark launcher so it can be run as part of the full benchmark suite.
