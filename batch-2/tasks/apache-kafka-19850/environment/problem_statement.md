## Description

The Kafka producer's built-in partitioner does not currently support rack awareness. In cloud and multi-datacenter deployments, producers and brokers are often distributed across multiple availability zones or racks. Without rack-aware partitioning, the producer has no way to prefer partitions whose leaders are co-located in the same zone, resulting in unnecessary cross-zone network traffic that increases both latency and cost.

## Expected Behavior

- When a producer is configured with its own rack/availability-zone identifier, the built-in partitioner should preferentially select partitions whose leader brokers are in the same rack as the producer.
- If all partitions in the producer's local rack become temporarily unavailable, the partitioner should automatically fall back to partitions in other racks to ensure continued availability.
- Once local-rack partitions become available again, the partitioner should resume preferring them over non-local ones.
- If broker nodes do not advertise rack information, the partitioner should behave as if rack awareness is disabled and continue selecting from all available partitions normally.
- Adaptive load-based partitioning should also respect rack awareness: when rack info is available, the load-balancing logic should focus on rack-local partitions. A mechanism should exist to expose the size of the rack-local portion of the load stats table.

## Why This Matters

Cross-zone network traffic in cloud environments is often metered and charged separately. Producers that are unaware of topology may unintentionally route records across availability zones, increasing costs and round-trip latency. Rack-aware partitioning allows operators to configure producers to stay local whenever possible, improving both performance and cost efficiency without sacrificing availability when local partitions go offline.
