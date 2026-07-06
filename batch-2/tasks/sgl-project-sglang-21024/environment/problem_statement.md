## Description

The tensor comparison debug utility fails with spurious validation errors when used in any configuration that includes data parallelism alongside other parallelism strategies (e.g., tensor parallelism or context parallelism). Because data parallelism splits the batch dimension across workers rather than sharding any specific tensor dimension, it is handled upstream before reaching the comparison logic. However, the validation layer has no awareness of this and raises an error claiming the data-parallel dimension is "undeclared," even though it is intentionally excluded from the comparison.

A related problem exists for sub-parallelism axes that are mathematically derived from a parent axis — for example, attention-specific or mixture-of-experts-specific variants of tensor parallelism. Because their rank is always determined by their parent's rank, they do not require independent declaration or handling. Yet the validator currently treats them as independent undeclared axes and raises errors.

## Expected Behavior

- When a data-parallel axis is filtered out upstream, the comparison tool should recognize this and skip validation for that axis, producing plans only for the genuinely sharded axes.
- Axes whose rank is uniquely determined by a declared parent axis should be treated as implicitly handled and should not trigger undeclared errors.
- The tool should correctly support configurations combining data parallelism with tensor and/or context parallelism, as well as configurations using tensor parallelism together with dependent sub-axes (attention-specific or MoE-specific variants).
- Genuinely undeclared independent axes (those not dependent on any declared parent and not filtered by the upstream data-parallel pass) must still raise appropriate validation errors.

## Why This Matters

Mixed-parallelism configurations combining data parallelism with tensor or context parallelism are common in large model inference. The tool becoming unusable in these configurations severely limits its practical value. This change makes the comparison utility work correctly across the full range of real-world multi-dimensional parallel setups.
