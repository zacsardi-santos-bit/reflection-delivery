## Description

The sketch-based aggregators used for distinct count operations in the star-tree index builder have a design problem: they eagerly compact intermediate merged results into finalized sketch objects after every aggregation step. This means that when building a star-tree index, every call to merge two sketches materializes a brand-new compacted sketch, even though further merges will happen immediately afterward. This unnecessary compaction wastes CPU and memory.

A related problem is that the method reporting the maximum aggregated byte size dynamically tracks the largest compacted sketch seen during processing, rather than returning a statically known upper bound. This makes the reported maximum size dependent on what data has been processed so far, which is unreliable for pre-allocation decisions.

## Expected Behavior

- The aggregators should accumulate data using union objects internally and only finalize (compact) into a sketch when serialization is requested.
- The aggregated value returned by the core aggregation methods (initial value creation, raw value application, aggregated value application, and clone operations) may be either a union or a sketch object — callers should handle both cases.
- The method reporting the maximum aggregated byte size should return a statically determined upper bound based on the aggregator's configuration, not the largest size observed at runtime.
- The default precision parameter for the integer tuple sketch variant should be consistent with the other sketch types (2^14 nominal entries), rather than the larger value (2^16) previously used.

## Why This Matters

Returning consistent, configuration-based byte size estimates makes star-tree pre-allocation predictable and avoids unnecessarily large memory allocations. Keeping intermediate results as open union objects reduces the number of compaction operations and allows the library to optimize merges more efficiently.
