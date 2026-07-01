I'm working on a stream processing pipeline where events carry time ranges — each event has a start time, an end time, and a current timestamp. Multiple upstream rules can produce overlapping ranges, so the same portion of time gets reported more than once. I need a processing node that keeps track of which time intervals have already been seen and, for each new incoming event, outputs only the portions of its range that haven't been covered before.

The node should maintain a persistent history of processed intervals and merge them as new ones arrive. It should also expire old intervals that fall outside a configurable time window so the history doesn't grow unboundedly. If an incoming interval is entirely within the expiration window boundary, it should produce no output. If it's partially expired, only the non-expired portion should be considered for deduplication.

Another important requirement is ordering: because events can arrive out of order, the node should use a priority queue to ensure results are emitted in ascending order of the interval's end time, not in the order events arrive. The priority queue should be orderable by the end-time value of each pending request.

The deduplication logic and the priority queue should live in the existing node package alongside the other processing node implementations.
