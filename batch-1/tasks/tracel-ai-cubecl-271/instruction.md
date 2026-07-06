Implement a producer-consumer execution strategy for a GPU matrix multiplication library to enable pipelined data loading and computation. Update existing modules and configurations to support this new strategy, ensuring compatibility with current tests and introducing new configurations for enhanced performance.

*   Replace the `stage::row_accumulate` module with `stage::multi_buffer`.
    *   Location: `crates/cubecl-linalg/src/matmul/components/stage/multi_buffer/`
    *   Expose `Matmul` struct with generic parameters `<I, O, Acc, TMM, SS>`.
    *   Ensure numerically correct results for existing homogeneous matmul tests.
    *   Re-export `LhsReader` and `RhsReader` types.

*   Create a new `stage::single_buffer` module.
    *   Location: `crates/cubecl-linalg/src/matmul/components/stage/single_buffer/`
    *   Expose `Matmul` struct with generic parameters `<I, O, Acc, TMM, SS>`.
    *   Re-export `LhsBufferReader` and `RhsBufferReader` types.

*   Develop a `global::producer_consumer` module.
    *   Location: `crates/cubecl-linalg/src/matmul/components/global/producer_consumer/`
    *   Expose `Matmul` struct with generic parameters `<EG, ES, SMM>`.
    *   Implement the `global::Matmul` trait.
    *   Use `stage::single_buffer::Matmul` as the `StageMatmul` type parameter.
    *   Support multi-buffer configurations with k-dimension stages of 2 and 3.

*   Update the `AdvancedConfig` struct.
    *   Replace `tiling_order: TilingOrderConfig` with `lhs_tiling_order: TilingOrderConfig` and `rhs_tiling_order: TilingOrderConfig`.
    *   Default both to `TilingOrderConfig::RowMajor`.

*   Modify the `TilingOrderConfig` enum.
    *   Replace `XMajor` with `RowMajor` and `YMajor` with `ColMajor`.
    *   Update tests to use `TilingOrderConfig::ColMajor` where applicable.

*   Add new stage size type aliases in `crates/cubecl-linalg/src/matmul/components/stage/base.rs`.
    *   Include `S1x1x3`, `S1x2x2`, and `S2x1x2` using the `create_cmma_stage!` macro.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.