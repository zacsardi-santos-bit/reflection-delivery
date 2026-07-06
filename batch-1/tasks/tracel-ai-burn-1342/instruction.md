Implement two reduction operations in the burn tensor library to check whether any or all elements in a tensor are non-zero or true. Provide both global and dimension-wise variants for these operations, applicable to floating-point, integer, and boolean tensor types.

*   Implement the `any()` method on the Tensor type:
    *   Location: `crates/burn-tensor/src/tensor/api/`
    *   Signature: `fn any(self) -> Tensor<B, 1, Bool>`
    *   Functionality: Reduce all elements across all dimensions to a single boolean tensor. Return true if at least one element is non-zero or true.

*   Implement the `all()` method on the Tensor type:
    *   Location: `crates/burn-tensor/src/tensor/api/`
    *   Signature: `fn all(self) -> Tensor<B, 1, Bool>`
    *   Functionality: Reduce all elements across all dimensions to a single boolean tensor. Return true only if every element is non-zero or true.

*   Implement the `any_dim(dim: usize)` method on the Tensor type:
    *   Location: `crates/burn-tensor/src/tensor/api/`
    *   Signature: `fn any_dim(self, dim: usize) -> Tensor<B, D, Bool>`
    *   Functionality: Reduce along the specified dimension and return a boolean tensor with that dimension collapsed. Each output position is true if at least one element along that slice is non-zero or true.

*   Implement the `all_dim(dim: usize)` method on the Tensor type:
    *   Location: `crates/burn-tensor/src/tensor/api/`
    *   Signature: `fn all_dim(self, dim: usize) -> Tensor<B, D, Bool>`
    *   Functionality: Reduce along the specified dimension and return a boolean tensor with that dimension collapsed. Each output position is true only if all elements along that slice are non-zero or true.

*   Update the test generation system:
    *   Implement the `testgen_any!()` macro:
        *   Location: `crates/burn-tensor/src/`
        *   Functionality: Generate tests for `any()` and `any_dim()` operations. Invoke within the `testgen_all!` macro in `crates/burn-tensor/src/tests/mod.rs`.
    *   Implement the `testgen_all_op!()` macro:
        *   Location: `crates/burn-tensor/src/`
        *   Functionality: Generate tests for `all()` and `all_dim()` operations. Invoke within the `testgen_all!` macro in `crates/burn-tensor/src/tests/mod.rs`.

*   Declare test modules:
    *   `mod all;` in `crates/burn-tensor/src/tests/ops/mod.rs`
        *   Test file: `crates/burn-tensor/src/tests/ops/all.rs`
        *   Attribute: `#[burn_tensor_testgen::testgen(all_op)]`
    *   `mod any;` in `crates/burn-tensor/src/tests/ops/mod.rs`
        *   Test file: `crates/burn-tensor/src/tests/ops/any.rs`
        *   Attribute: `#[burn_tensor_testgen::testgen(any)]`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.