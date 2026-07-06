# Filter Synchronization Across Distributed Workers

## Description

When running reinforcement learning with multiple parallel workers, each worker accumulates its own observation and reward normalization statistics independently. Currently, there is no mechanism to aggregate those statistics back to a central location and redistribute a consistent, up-to-date state to all workers. This leads to workers normalizing data using divergent statistics, which can negatively impact training stability and correctness.

## Expected Behavior

- There should be a centralized manager capable of collecting normalization statistics from all remote workers, merging them into a unified state, and pushing the updated state back to every worker.
- The normalization filters themselves should support operations to apply accumulated changes from another filter (with or without also copying the buffer), to clear their internal buffer, and to synchronize all state from another filter.
- The running statistics tracker underlying the filters should correctly compute mean, variance, and standard deviation, and should support merging two independent trackers into one equivalent combined tracker.
- An asynchronous gradient optimizer should correctly accumulate gradients from remote workers and apply a configurable number of gradient updates per training step to the local model.

## Why This Matters

Consistent normalization across all workers is essential for stable distributed training. Without synchronized filter state, workers may apply incompatible normalizations to their observations and rewards, degrading learning performance. A well-tested filter management utility alongside a verified asynchronous optimizer ensures that the distributed training infrastructure behaves correctly and predictably.
