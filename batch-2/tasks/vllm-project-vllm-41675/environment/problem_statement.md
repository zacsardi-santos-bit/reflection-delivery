## Description

When using the optimized all-reduce communication path on ROCm multi-GPU hardware, there is currently no way to override the minimum tensor size threshold that governs when the path activates. The threshold is entirely determined by a hardcoded lookup table based on data type, world size, and quantization level. Additionally, the quantization codec is always applied regardless of tensor size — there is no mechanism to fall back to unquantized (full-precision) transmission for smaller tensors where quantization overhead may outweigh any benefit.

## Expected Behavior

- Users should be able to set an environment variable (in megabytes) to override the minimum tensor size required to use the optimized all-reduce path. When this override is not set, the existing built-in threshold table should continue to be used.
- Users should be able to set a separate environment variable (in kilobytes) to define a minimum tensor size below which the operation skips quantization and uses full-precision transmission instead. Tensors at or above this threshold continue to use the configured quantization codec.
- Adjusting the quantization threshold must not affect which tensors are eligible for the optimized path — only whether quantization is applied when they use it.
- Both environment variables must be validated: negative values should be rejected with an appropriate error. The size override must also be rejected when it exceeds the effective maximum size.

## Why This Matters

These controls allow users to tune the trade-off between communication bandwidth and quantization overhead for their specific models and hardware, without requiring code changes. Workloads with many small tensors can disable quantization for those tensors while still benefiting from the optimized all-reduce path for larger ones.
