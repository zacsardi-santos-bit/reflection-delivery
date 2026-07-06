## Description

The distributed training library needs cleaner, more consistent APIs for working with multi-process, multi-device setups. Currently there is no standard way for a process to know which shard of the training data it should consume, and the internal attribute tracking process count is inconsistently named (using a grammatically incorrect singular form).

## Expected Behavior

- Both the data-parallel and model-parallel distribution strategies should expose a property that reports how many complete copies of the model exist across all devices.
- The distribution base class should expose a property that automatically computes which data shard the current process is responsible for, based on the number of model replicas and the total number of processes. When there are at least as many model replicas as processes, each process gets its own shard. When there are more processes than model replicas, multiple processes share the same shard.
- The process-count attribute and property should use consistent, grammatically correct naming throughout (plural form), including in validation error messages.

## Why This Matters

Without a standard way to determine data shard assignment, users must manually implement this logic — and risk getting it wrong in the common case where the number of processes differs from the number of model replicas. Providing these properties as first-class API makes distributed data loading straightforward and correct for all distribution strategies.
