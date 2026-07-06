I'm working on a set of tensor alignment and unsharding utilities that currently track dimension positions using integer indices passed explicitly at every call site.

*   The dims module must export a parse_dim_names function that accepts a dimension specification string and returns a list of plain dimension name strings, stripping all modifier annotations such as parallel axis, ordering, or reduction markers. For example, 'b s h d' returns ['b', 's', 'h', 'd'] and 'b s(cp,zigzag) h(tp) d' also returns ['b', 's', 'h', 'd'].

*   The dims module must export a resolve_dim_by_name function that accepts a named PyTorch tensor and a dimension name string, and returns the integer index of that dimension. If the name is not found in the tensor's names, a ValueError must be raised with a message that includes the phrase 'not in tensor names'. If the tensor has no names at all (all None), a ValueError must be raised with a message that includes the phrase 'no names'.

*   The dims module must export an apply_dim_names function that accepts a tensor and a list of name strings, and returns the tensor with those names applied as its dimension names. The output tensor must have the specified names, the same shape as the input, and identical data values.

*   The dims module must export a strip_dim_names function that accepts a tensor (named or unnamed) and returns a tensor with all dimension names set to None. This function must not raise an error when called on an already-unnamed tensor.

*   The DimSpec class must be exported from the dims module and must have a name attribute (str) that holds the plain dimension name without any modifiers.

*   The ConcatParams class must replace its dim attribute (integer dimension index) with a dim_name attribute (string dimension name). The constructor must accept dim_name as a keyword argument, e.g. ConcatParams(dim_name='h'). Existing code that reads plans[0].params.dim_name must return the string name of the concatenation axis.

*   The reorderer plan's params object must replace its dim attribute (integer) with a dim_name attribute (string), so that plans[0].params.dim_name returns the string dimension name (e.g. 's') rather than an integer index.

*   The execute_token_aligner function must no longer require a token_dims parameter. Input tensors must be named (with PyTorch named tensor dimensions), and the function must determine the token dimension from the tensor's named dimension 't' automatically. Callers that previously passed token_dims=Pair(x=N, y=N) must be able to call the function without that argument.

*   The AlignerPlan data structure must not include a token_dims field. Code that previously set token_dims=Pair(x=N, y=N) on AlignerPlan must no longer do so, and the field must not be present.

*   The execute_unsharder_plan function must accept input tensors that have named dimensions (applied via refine_names or apply_dim_names) and must return named tensors. Callers comparing output values with plain tensors must call rename(None) or equivalent stripping before the comparison.


*   Interface details: Type: Function
Name: parse_dim_names
Location: python/sglang/srt/debug_utils/comparator/dims.py
Signature: parse_dim_names(dim_string: str) -> list[str]
Description: Parses a dimension specification string and returns only the plain dimension names, stripping all modifiers (e.g. parallel axis, ordering, reduction annotations). For example, "b s(cp,zigzag) h(tp) d" returns ["b", "s", "h", "d"].

Type: Function
Name: resolve_dim_by_name
Location: python/sglang/srt/debug_utils/comparator/dims.py
Signature: resolve_dim_by_name(tensor: torch.Tensor, name: str) -> int
Description: Given a named PyTorch tensor, returns the integer index of the dimension whose name matches the given string. Raises ValueError with a message matching "not in tensor names" if the dimension name is not present in the tensor's names. Raises ValueError with a message matching "no names" if the tensor has no names at all (all None).

Type: Function
Name: apply_dim_names
Location: python/sglang/srt/debug_utils/comparator/dims.py
Signature: apply_dim_names(tensor: torch.Tensor, names: list[str]) -> torch.Tensor
Description: Returns the tensor with the given list of strings applied as its dimension names. Shape and data are preserved. Equivalent to calling refine_names but accepting a plain list of strings.

Type: Function
Name: strip_dim_names
Location: python/sglang/srt/debug_utils/comparator/dims.py
Signature: strip_dim_names(tensor: torch.Tensor) -> torch.Tensor
Description: Returns the tensor with all dimension names removed (all set to None). If the tensor is already unnamed, the result is also unnamed with all-None names. No error is raised for unnamed tensors.

Type: Class
Name: DimSpec
Location: python/sglang/srt/debug_utils/comparator/dims.py
Description: Represents a single parsed dimension specification. Must be exported from the dims module. Has a name attribute (str) that holds the plain dimension name (without modifiers).
Signature: name: str  (attribute)

Type: Class
Name: ConcatParams
Location: python/sglang/srt/debug_utils/comparator/aligner/unsharder/types.py
Description: Parameters for a concatenation unsharding operation. The dim attribute (int) is replaced by dim_name (str), which holds the string name of the dimension along which to concatenate. Must accept dim_name as a keyword argument at construction time (e.g. ConcatParams(dim_name="h")).
Signature: dim_name: str  (attribute, replaces the former dim: int attribute)

Type: Class
Name: AlignerPlan
Location: python/sglang/srt/debug_utils/comparator/aligner/entrypoint/types.py
Description: Plan for the full aligner pipeline. The token_dims field (Pair[int]) is removed. AlignerPlan no longer carries token dimension indices; they are resolved from named tensor dimensions at execution time.

Type: Function
Name: execute_token_aligner
Location: python/sglang/srt/debug_utils/comparator/aligner/token_aligner/executor.py
Signature: execute_token_aligner(plan: TokenAlignerPlan, tensor_of_step_pair: Pair[dict[int, torch.Tensor]]) -> Pair[torch.Tensor]
Description: Executes token alignment. The token_dims parameter (previously Pair[int]) is removed. The function now determines the token dimension by reading the named dimension "t" from the input tensors. Input tensors must have named dimensions. Calling with the old token_dims keyword argument must no longer be required.

Note on Reorderer plan params: The reorderer plan's params object also has dim_name: str replacing the former dim: int attribute. Tests assert plans[0].params.dim_name == "s" where dim=1 was expected before.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.