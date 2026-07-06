## Add Producer-Consumer Global Matmul Strategy

### Description

The current GPU matrix multiplication library only supports a homogeneous execution strategy: every compute unit loads data and performs computation in lockstep. This prevents the data loading and compute stages from overlapping, leaving performance on the table.

We need a producer-consumer execution strategy where a subset of GPU work units specialize in loading matrix tiles into shared buffers, while the remaining units perform matrix multiply-accumulate operations concurrently on already-loaded data. This enables double-buffering and triple-buffering pipelines where loading and computation overlap.

### What Needs to Change

- The existing stage-level implementation (previously called "row accumulate") should be renamed to "multi buffer" to better reflect its purpose.
- A new "single buffer" stage variant should be introduced, designed for pipelined use — it works on one buffer at a time and is the stage implementation for the producer-consumer global strategy.
- A new "producer-consumer" global matmul module should be created where some planes load data and others compute.
- The tiling order configuration should be split into independent controls for left-hand side and right-hand side operands (instead of a single combined setting).
- The tiling order variant names should be updated to use clearer row-major and column-major naming (replacing the previous naming convention).

### Why This Matters

This change allows the matmul framework to be used with pipelined execution strategies that can significantly improve GPU utilization by hiding memory latency behind computation.

### Expected Behavior

- All existing homogeneous matmul tests should continue to pass using the renamed stage implementation.
- New producer-consumer tests should pass for various matrix sizes, batch dimensions, and multi-stage configurations (k-dimension stages of 2 and 3 for double- and triple-buffering).
- The tiling configuration API should allow independent tiling order choices per operand.
