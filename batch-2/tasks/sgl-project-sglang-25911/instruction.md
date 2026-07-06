I'm working on the debug comparator utilities in this codebase, which use a dimension naming system to track what each axis of a tensor represents (token dimension, batch, hidden, etc.

*   A new function `get_dim_names` must exist in `python/sglang/srt/debug_utils/comparator/dims_spec/tensor_naming.py` and be exported from `python/sglang/srt/debug_utils/comparator/dims_spec/__init__.py`. It must accept a `torch.Tensor` and return `tuple[Optional[str], ...]`: the attached dimension names if present, or `(None,) * tensor.ndim` if no names have been attached.

*   A function `without_dim_names` must exist in `python/sglang/srt/debug_utils/comparator/dims_spec/tensor_naming.py` and be exported from `python/sglang/srt/debug_utils/comparator/dims_spec/__init__.py`. It must accept a `torch.Tensor` and return a new tensor such that calling `get_dim_names` on the result returns a tuple of `None` values. The previously exported function `strip_dim_names` must be removed from `__all__` and must not be importable from `sglang.srt.debug_utils.comparator.dims_spec`.

*   The function `apply_dim_names` must be modified so that it does NOT use PyTorch's native named tensor mechanism (i.e., must not call `tensor.refine_names()`). The returned tensor must support standard operations such as `reshape` and einops `rearrange` without first stripping dimension names. After calling `apply_dim_names(tensor, names)`, `get_dim_names` on the result must return the same names as a tuple of strings.

*   The function `apply_dim_names` must raise a `ValueError` with a descriptive message when the length of `dim_names` does not match `tensor.ndim`.

*   Calling `without_dim_names` on a tensor produced by `apply_dim_names` must preserve the original tensor data: the result must be numerically equal to the original tensor.

*   The function `resolve_dim_by_name` must work correctly with tensors whose names were applied via `apply_dim_names`. When the requested dimension name is not found in the tensor's names, it must raise `ValueError` with a message matching the pattern `'not in tensor names'`. When the tensor has no names (i.e., `get_dim_names` returns all-`None`), it must raise `ValueError` indicating the tensor has no names.

*   Both `get_dim_names` and `without_dim_names` must appear in `__all__` in `python/sglang/srt/debug_utils/comparator/dims_spec/__init__.py` and must be importable directly from `sglang.srt.debug_utils.comparator.dims_spec`.


*   Interface details: Type: Function
Name: get_dim_names
Location: python/sglang/srt/debug_utils/comparator/dims_spec/tensor_naming.py
Signature: get_dim_names(tensor: torch.Tensor) -> tuple[Optional[str], ...]
Description: Returns the dimension names attached to a tensor. If no names have been attached (via the custom naming mechanism), returns a tuple of None values with length equal to tensor.ndim. Must also be exported from python/sglang/srt/debug_utils/comparator/dims_spec/__init__.py and listed in __all__.

Type: Function
Name: without_dim_names
Location: python/sglang/srt/debug_utils/comparator/dims_spec/tensor_naming.py
Signature: without_dim_names(tensor: torch.Tensor) -> torch.Tensor
Description: Returns a new tensor view without any attached dimension names. After calling this, get_dim_names on the result returns a tuple of None values. Replaces the removed strip_dim_names function. Must also be exported from python/sglang/srt/debug_utils/comparator/dims_spec/__init__.py and listed in __all__.

Type: Function
Name: apply_dim_names
Location: python/sglang/srt/debug_utils/comparator/dims_spec/tensor_naming.py
Signature: apply_dim_names(tensor: torch.Tensor, dim_names: list[str]) -> torch.Tensor
Description: Attaches dimension names to a tensor using a custom attribute-based mechanism (NOT PyTorch's native refine_names). The returned tensor must support all standard PyTorch operations (reshape, arithmetic, etc.) without requiring name removal first. Names applied with this function must be retrievable via get_dim_names. Raises ValueError if len(dim_names) != tensor.ndim. Already exported from python/sglang/srt/debug_utils/comparator/dims_spec/__init__.py.

Note: strip_dim_names must be removed from __all__ in python/sglang/srt/debug_utils/comparator/dims_spec/__init__.py and must no longer be importable from that module.

The custom naming mechanism stores names as a _dim_names attribute (tuple[Optional[str], ...]) directly on a tensor view. get_dim_names reads this attribute (getattr(tensor, "_dim_names", None)) and falls back to (None,) * tensor.ndim when absent.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.