Implement new SQL functions for vector arithmetic in the database to support element-wise subtraction, element summation, and column aggregation. Make internal vector conversion utilities publicly accessible for testing purposes and ensure the linear algebra library is a shared dependency.

*   Change the visibility of the `impl_conv` submodule to public in `src/common/function/src/scalars/vector.rs` and `src/common/function/src/scalars/vector/impl_conv.rs`.
    *   Ensure functions `veclit_to_binlit`, `as_veclit`, and `as_veclit_if_const` are accessible from external crates.
*   Register a new SQL aggregate function `VEC_SUM`:
    *   Accepts a column of binary-encoded float32 vectors.
    *   Returns a binary-encoded float32 vector representing the element-wise sum.
    *   Returns Null if any row in the group is null.
*   Register a new SQL scalar function `vec_sub`:
    *   Accepts two vector arguments (string literal or binary-encoded).
    *   Returns a binary-encoded float32 vector with element-wise differences.
    *   Example: `vec_sub('[1.0, 1.0]', '[1.0, 2.0]')` should produce `[0,-1]`.
*   Register a new SQL scalar function `vec_elem_sum`:
    *   Accepts a single vector argument (string literal or binary-encoded).
    *   Returns a float32 scalar equal to the sum of all elements.
    *   Example: `vec_elem_sum('[1.0, 2.0, 3.0]')` should return `6.0`.
*   Add `nalgebra` as a workspace-level dependency and as a dev-dependency in the `query` crate's `Cargo.toml`.
*   Implement a public helper function `create_query_engine_for_vector10x3` in `src/query/src/tests/function.rs`:
    *   Returns a `QueryEngineRef` with an in-memory table named `vectors`.
    *   Table contains a single binary column named `vector` with 10 randomly generated 3-dimensional float32 vectors.
*   Implement and register `VectorSumCreator` and `VectorSum` in `src/common/function/src/scalars/vector/sum.rs`:
    *   `VectorSumCreator` must create `VectorSum` accumulators and be registered as "vec_sum".
    *   `VectorSum` must implement the `Accumulator` interface and handle null values appropriately.
*   Implement `SubFunction` in `src/common/function/src/scalars/vector/sub.rs`:
    *   Implements the `Function` trait for "vec_sub".
    *   Uses `nalgebra` for element-wise subtraction.
*   Implement `ElemSumFunction` in `src/common/function/src/scalars/vector/elem_sum.rs`:
    *   Implements the `Function` trait for "vec_elem_sum".
*   Declare new modules `sub` and `elem_sum` in `src/common/function/src/scalars/vector.rs` for the respective functions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.