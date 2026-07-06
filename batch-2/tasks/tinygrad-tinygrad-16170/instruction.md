I'm trying to use masked element selection and non-zero index lookup inside a JIT-compiled function, but both operations fail at compile time because their output shapes are dynamic — they depend on how many elements match at runtime, which the compiler can't know ahead of time.

*   The masked_select method on Tensor must accept two new optional parameters: size (int or None, default None) and fill_value (a constant scalar value, default 0).

*   When size is None, masked_select behaves as before (dynamic output length equal to the number of True values in mask, not JIT-compatible).

*   When size is provided to masked_select, the output always has exactly size elements: if fewer elements match the mask, the remaining positions are filled with fill_value; if more elements match, the output is truncated to size elements.

*   The output dtype of masked_select must always equal the dtype of the input tensor (self.dtype), regardless of the type of fill_value. A float fill_value on a float tensor keeps float dtype; a float fill_value on an integer tensor must not promote the dtype.

*   When size is provided to masked_select, the operation must be compatible with TinyJit (no dynamic shape resolution at JIT capture time), producing correct results across multiple JIT invocations with different inputs and different numbers of matching elements.

*   The nonzero method on Tensor must accept two new optional parameters: size (int or None, default None) and fill_value (a constant scalar value, default 0).

*   When size is None, nonzero behaves as before (dynamic output shape (n_nonzero, ndim), not JIT-compatible).

*   When size is provided to nonzero, the output has shape (size, ndim): if fewer elements are non-zero, the remaining rows are filled with fill_value; if more elements are non-zero, the output is truncated to size rows.

*   For a 0-dimensional (scalar) tensor, nonzero(size=N) must return a tensor with shape (N, 0).

*   The output dtype of nonzero must always be the default integer type (dtypes.default_int) when size is provided, regardless of whether fill_value is a float. A float fill_value must not promote the output to a float dtype.

*   When size is provided to nonzero, the operation must be compatible with TinyJit (no dynamic shape resolution at JIT capture time), producing correct results across multiple JIT invocations with different inputs and different numbers of non-zero elements.


*   Interface details: Type: Method
Name: masked_select
Location: tinygrad/tensor.py
Signature: masked_select(self, mask, size: int | None = None, fill_value: ConstType = 0) -> Tensor
Description: Selects elements from self based on the boolean mask. When size is provided, output has exactly size elements — padded with fill_value if fewer match, or truncated if more match. Output dtype is always self.dtype. With size provided, the operation is JIT-compatible.

Type: Method
Name: nonzero
Location: tinygrad/tensor.py
Signature: nonzero(self, size: int | None = None, fill_value: ConstType = 0) -> Tensor
Description: Returns indices of non-zero elements. When size is provided, output has shape (size, ndim) — rows padded with fill_value if fewer elements are non-zero, or truncated if more match. For a 0-dim scalar tensor with size=N, output shape is (N, 0). Output dtype is always dtypes.default_int regardless of fill_value type. With size provided, the operation is JIT-compatible.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.