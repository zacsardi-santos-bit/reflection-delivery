## Description

The distributed computation utilities need two improvements to better support multi-GPU machine learning pipelines.

First, the existing hierarchical tree-reduction helper only works with lazily-constructed computation graphs; it has no way to accept already-submitted distributed tasks (futures). Additionally, callers cannot specify which combining function should be used at each step of the reduction — the combining logic is fixed internally. This limits flexibility when pipelines need to express different kinds of aggregation or when intermediate results have already been submitted to workers.

Second, there is no general flat-reduction helper that accepts a collection of already-running distributed tasks and a user-supplied combining function to fold them into a single result.

## Expected Behavior

- The hierarchical tree-reduction helper should accept an optional combining function as a parameter, so callers can control how values are merged at each level of the tree.
- The hierarchical tree-reduction helper should accept distributed futures (already-submitted tasks) as input, not only lazily-defined computations.
- A new general-purpose reduction utility should be added that takes a list of distributed futures and a combining function, and returns a computable result representing the aggregation.

## Why This Matters

Multi-GPU workloads often submit partial computations eagerly to workers and then need to reduce those results. Without support for futures and a user-specified combining function, callers are forced to restructure how they launch tasks or work around the limitations in ad-hoc ways. These additions allow distributed pipelines to compose naturally.
