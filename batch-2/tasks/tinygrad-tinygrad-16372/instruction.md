I'm running into a confusing inconsistency in tinygrad's constant-fill tensor factories.

*   Tensor.ones(), Tensor.zeros(), and Tensor.full() must accept a `buffer` keyword argument (default True). When buffer=True (the default), calling .realize() on the result must produce a tensor whose uop.is_realized property is True — meaning it is backed by actual allocated memory, consistent with how all other realized tensors behave.

*   When buffer=False is passed to Tensor.ones(), Tensor.zeros(), or Tensor.full(), the resulting tensor must behave as a broadcast constant with no memory allocation. Specifically, indexing into such a tensor requires zero schedule operations (check_schedule returns count 0).

*   The internal UOp.unique_const and Tensor.unique_const static methods must be removed. The Tensor.full() implementation must produce a materialized buffer (via clone or equivalent) when buffer=True, rather than routing through unique_const.

*   After calling Tensor.ones(4, 4).realize(), the resulting tensor's uop.is_realized property must be True.

*   The schedule step count for a fold_conv_batchnorm optimizer step with SGD must be exactly 15 when the input image and optimizer parameters are pre-realized before the schedule check.

*   The schedule step count for a fold_conv_batchnorm optimizer step with Adam must be exactly 29 when the input image and optimizer parameters are pre-realized before the schedule check.

*   The schedule step count for a batchnorm backward pass (through a convolutional network) must be exactly 10 when BatchNorm parameters are pre-realized.


*   Interface details: Type: ClassMethod
Name: full
Location: tinygrad/mixin/__init__.py
Signature: full(cls, shape: tuple, fill_value, dtype=None, device=None, buffer=True) -> Self
Description: Creates a tensor filled with fill_value. When buffer=True (default, new behavior), produces a materialized buffer-backed tensor by cloning into storage — after calling .realize() on the result, uop.is_realized is True. When buffer=False, produces a broadcast constant with no memory allocation (old behavior) — uop.is_realized remains False after .realize(). The `buffer` parameter replaces the old behavior that always produced a unique-const broadcast.

Note: Tensor.ones(), Tensor.zeros() forward their keyword arguments (including `buffer`) to Tensor.full(), so they inherit this parameter automatically. No separate interface change is needed for them.

Note: The UOp.unique_const and Tensor.unique_const static methods are removed as part of this change. The full() implementation must no longer call unique_const; instead it must use clone() to materialize the buffer when buffer=True.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.