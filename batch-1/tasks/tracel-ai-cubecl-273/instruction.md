Implement a pluggable dispatch abstraction for batch matrix multiplication in a GPU linear algebra library. Update the batch matmul types to accept this dispatch strategy as an additional type parameter, ensuring existing code continues to function correctly with the default strategy.

*   Define a new trait `CubeDispatch` in `crates/cubecl-linalg/src/matmul/components/batch/cube_dispatch.rs`.
    *   Annotate with `#[cube]` and derive `Clone`, `Copy`, `Debug`, `Hash`, `Eq`, `PartialEq`.
    *   Require bounds: `Clone + Copy + 'static + Send + Sync + Debug + Hash + Eq`.
    *   Declare methods:
        *   `fn x_y_indices() -> (u32, u32)`
        *   `fn batch_index() -> u32`
        *   `fn max_x(#[comptime] cube_count: (u32, u32, u32)) -> u32`
        *   `fn max_y(#[comptime] cube_count: (u32, u32, u32)) -> u32`
        *   `fn max_batches(#[comptime] cube_count: (u32, u32, u32)) -> u32`

*   Implement four structs for `CubeDispatch` and export them:
    *   `NaturalDispatch`
        *   `x_y_indices` returns `(CUBE_POS_X, CUBE_POS_Y)`
        *   `batch_index` returns `CUBE_POS_Z`
        *   `max_x`, `max_y`, `max_batches` return `cube_count.0`, `cube_count.1`, `cube_count.2`
    *   `TransposedDispatch`
        *   `x_y_indices` returns `(CUBE_POS_Y, CUBE_POS_X)`
        *   `batch_index` returns `CUBE_POS_Z`
        *   `max_x` returns `cube_count.1`
        *   `max_y` returns `cube_count.0`
        *   `max_batches` returns `cube_count.2`
    *   `SwizzleNaturalDispatch<const W: u32>`
        *   Prioritizes x-axis with swizzle computation
        *   `max_x`, `max_y`, `max_batches` return `cube_count.0`, `cube_count.1`, `cube_count.2`
    *   `SwizzleTransposedDispatch<const W: u32>`
        *   Prioritizes y-axis with swizzle computation
        *   `max_x` returns `cube_count.1`
        *   `max_y` returns `cube_count.0`
        *   `max_batches` returns `cube_count.2`

*   Update `batch::one_to_one::Matmul`:
    *   Accept a fourth generic type parameter `C: CubeDispatch`.
    *   Use `C::x_y_indices()` and `C::batch_index()` in `execute`.

*   Update `batch::one_to_many::Matmul`:
    *   Accept a fifth generic type parameter `C: CubeDispatch`.
    *   Use `C::x_y_indices()` and `C::batch_index()` for `Span` construction.
    *   Use `C::max_x`, `C::max_y`, `C::max_batches` for cube count extents.

*   Ensure all existing call sites for `Matmul` types supply `NaturalDispatch` as the new type argument.

*   Validate that matrix multiplication produces correct results with:
    *   `TransposedDispatch` for swapped x and y dimensions.
    *   `SwizzleNaturalDispatch<2>` and `SwizzleTransposedDispatch<2>` for multi-batch problems.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.