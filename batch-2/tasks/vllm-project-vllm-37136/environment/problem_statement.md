## Description

When loading very large mixture-of-experts (MoE) language models with expert parallelism enabled, every GPU rank currently reads all expert weights from storage — even though each rank only uses a small fraction of the total experts. For models with hundreds of experts, this means each rank wastes the majority of its storage I/O loading tensors it will never use. Expert weights dominate the total parameter count in these models (often 85–90%), so this unnecessary loading is a serious bottleneck.

## Expected Behavior

- A utility should be available to determine which expert IDs belong to a given rank, given the total number of experts, the parallelism size, and the rank index.
- Both contiguous block assignment and interleaved (round-robin) expert placement strategies should be supported.
- A utility should be able to classify any weight tensor name as either a local expert weight, a non-local expert weight, or a non-expert weight (dense, shared expert, or fused tensor with no per-expert ID).
- The safetensors weight loading iterator should support an optional parameter that, when provided, causes non-local expert weights to be skipped before being read from disk.
- Fused expert tensors that store all experts in a single tensor without a per-expert numeric identifier in the name must never be filtered out — they require later slicing by the model.

## Why This Matters

This optimization can dramatically reduce storage I/O during model loading for large MoE models under expert parallelism, improving startup time and reducing unnecessary memory pressure.
