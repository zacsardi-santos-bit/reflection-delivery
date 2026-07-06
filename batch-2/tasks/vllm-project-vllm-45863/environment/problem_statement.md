## Description

When running inference with the DeepSeek V4 sparse attention implementation on NVIDIA hardware, the function that constructs mixed sparse token index tables is called multiple times per forward pass with identical batch metadata. This happens because both the sliding-window attention path and the coarser-grained sparse attention path each trigger the index build, even though both calls receive the same inputs and would produce the same output. The computation is expensive and the redundant rebuilding wastes time.

## Expected Behavior

- When the sparse index builder is invoked for the sliding-window-only attention path, and then invoked again with the same batch metadata in the same step, the second call should return the previously computed result from a cache rather than rerunning the build.
- The same cache reuse should apply for the high-compression-ratio sparse attention path.
- For the fine-grained (low compression ratio) sparse path, caching should be intentionally skipped because the index tables may legitimately differ between calls, making caching incorrect for that mode.

## Why This Matters

Eliminating the redundant index rebuild reduces unnecessary computation within each forward pass, improving inference throughput for models using this sparse attention mechanism.
