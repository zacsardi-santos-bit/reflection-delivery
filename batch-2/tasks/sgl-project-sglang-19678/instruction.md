I'm working on a tensor debugging and comparison tool for distributed model training.

*   The dimension modifier annotation syntax must use square brackets instead of parentheses. For example, 'h[tp]', 's[cp:zigzag]', 'h[tp:partial]', 't[cp:zigzag,sp]' are now the required forms; the old parenthesis forms must no longer be accepted as valid input.

*   parse_dim must accept fused dimension tokens of the form '(sub1*sub2)' or '(sub1*sub2*sub3)' with an optional modifier suffix '[axis]' or '[axis:qualifier]'. Parsing a fused token must produce a DimSpec with is_fused=True and sub_dims equal to the list of component names.

*   DimSpec must expose an is_fused boolean attribute (False for regular dims, True for fused dims) and a sub_dims list attribute (contains [name] for regular dims; contains the ordered list of sub-dimension names for fused dims).

*   DimSpec must expose a sanitized_name property. For regular dims it equals the dim name. For fused dims it equals the sub-dimension names joined by triple underscores ('___'), e.g. 'num_heads___head_dim' for a fused '(num_heads*head_dim)'.

*   parse_dim must raise ValueError matching 'Duplicate sub-dim' when a fused token contains a repeated sub-dimension name (e.g. '(a*a)'). It must raise ValueError matching 'Invalid sub-dim' when a sub-dimension is a numeric literal (e.g. '(a*1)'). It must raise ValueError matching 'Invalid dim token' when a squeeze dim (numeric literal) is given a modifier (e.g. '1[tp]').

*   parse_dims must raise ValueError matching 'Duplicate' when a fused dim's sub-dimension names conflict with the names of regular dims in the same specification, or when two fused dims in the same specification share a sub-dimension name.

*   resolve_dim_names must return sanitized_name for each fused dim, so '(num_heads*head_dim)' resolves to 'num_heads___head_dim', '(a*b*c)' resolves to 'a___b___c', and squeeze dims still resolve to their usual singleton names.

*   Plugin methods that infer dims for CP-sharded tensors must return strings using square bracket syntax (e.g. 't[cp:zigzag]' for 1D, 'b s[cp:zigzag]' for 2D).

*   compute_axis_aligner_plan must handle mixed fused/separate layouts: when one side has a fused dim '(a*b)' and the other has separate dims 'a b', a flatten einops pattern must be generated for the side with separate dims (e.g. 'a b c -> (a b) c'). When the fused side must also be reordered, its pattern uses sanitized_name for the fused token (e.g. 'c a___b -> a___b c').

*   compute_axis_aligner_plan must return None with exactly one warning when fused groups on the two sides are incompatible (mismatched names or overlapping sub-dim membership). The warning for overlapping fused groups must have category equal to 'axis_aligner_fused_conflict' and a message containing 'overlapping fused groups'.

*   execute_axis_aligner_plan must support einops flatten patterns (e.g. 't nh hd -> t (nh hd)') producing correctly shaped tensors. It must raise ValueError with a message matching 'side must be' when the 'side' argument is not a valid side identifier.

*   compute_unsharder_plan must handle fused dims with parallel modifiers: a fused dim annotated '[tp]' must produce a ConcatParams plan with dim_name equal to the sanitized_name of the fused dim. A fused dim annotated '[tp:partial]' must produce a ReduceSumParams plan. A fused dim with no parallel modifier is treated as replicated and produces a PickParams plan.

*   execute_unsharder_plan must use DimSpec.sanitized_name (not DimSpec.name) when assigning named-tensor dimension labels during unsharding.


*   Interface details: Type: Class
Name: DimSpec
Location: sglang/srt/debug_utils/comparator/dims.py (or equivalent module under sglang/srt/debug_utils/comparator/)
Description: Represents a single parsed dimension specification. Must expose the following attributes in addition to existing ones:
  - is_fused: bool — True when this dim was parsed from fused syntax like (a*b); False for regular dims.
  - sub_dims: list[str] — For regular dims, [name]. For fused dims, the ordered list of sub-dimension names.
  - sanitized_name: str (property) — For regular dims, equals name. For fused dims, equals sub_dims joined by '___' (triple underscore), e.g. 'num_heads___head_dim'.

Type: Function
Name: parse_dim
Location: sglang/srt/debug_utils/comparator/dims.py (or equivalent)
Signature: parse_dim(token: str) -> DimSpec
Description: Parse a single dimension token using square-bracket modifier syntax. Accepts:
  - Plain names: "b", "h", "t"
  - Annotated names with square brackets: "h[tp]", "s[cp:zigzag]", "h[tp:partial]", "t[cp:zigzag,sp]"
  - Fused dims: "(num_heads*head_dim)", "(a*b*c)"
  - Fused dims with modifiers: "(num_heads*head_dim)[tp]", "(a*b)[cp:zigzag]"
  Raises ValueError matching "Invalid dim token" for empty brackets or nested brackets, and for numeric literals with modifiers like "1[tp]".
  Raises ValueError matching "Unknown axis" for unrecognized axis labels.
  Raises ValueError matching "Unknown qualifier" for unrecognized qualifier labels.
  Raises ValueError matching "Multiple ordering" for duplicate ordering qualifiers.
  Raises ValueError matching "Multiple reduction" for duplicate reduction qualifiers.
  Raises ValueError matching "Duplicate axis" for repeated axis labels.
  Raises ValueError matching "Duplicate sub-dim" for fused dims with repeated sub-names like "(a*a)".
  Raises ValueError matching "Invalid sub-dim" for fused dims with numeric sub-dims like "(a*1)".

Type: Function
Name: parse_dims
Location: sglang/srt/debug_utils/comparator/dims.py (or equivalent)
Signature: parse_dims(dims_str: str) -> DimsSpec
Description: Parse a full dimension specification string (space-separated tokens) using square-bracket syntax. Supports fused dimensions alongside regular dimensions. Raises ValueError matching "Duplicate" when any sub-dimension name from a fused dim conflicts with a regular dim name or another fused dim's sub-dimension names in the same specification.

Type: Function
Name: resolve_dim_names
Location: sglang/srt/debug_utils/comparator/dims.py (or equivalent)
Signature: resolve_dim_names(dims_str: str) -> list[str]
Description: Resolve a dims string to a list of physical tensor axis labels. Fused dims resolve to their sanitized_name (triple-underscore-joined sub-dim names). Squeeze dims (numeric literals like "1") resolve to singleton names as before. Hash-section declarations are stripped.

Type: Function
Name: compute_axis_aligner_plan
Location: sglang/srt/debug_utils/comparator/aligner/axis_aligner.py (or equivalent)
Signature: compute_axis_aligner_plan(pair: Pair) -> Optional[AxisAlignerPlan]
Description: Compute an alignment plan for two dimension specifications. Extended to handle fused vs. separate layouts:
  - When one side has a fused dim '(a*b)' and the other has the matching separate dims 'a b', generates a flatten einops pattern for the separate side (e.g. 'a b c -> (a b) c').
  - When the fused side also needs reordering, uses sanitized_name in the pattern (e.g. 'c a___b -> a___b c').
  - Returns None with a warning when fused group names do not match across sides.
  - Returns None with a warning (category='axis_aligner_fused_conflict', message contains 'overlapping fused groups') when fused sub-dims on the two sides overlap but are not identical.

Type: Function
Name: execute_axis_aligner_plan
Location: sglang/srt/debug_utils/comparator/aligner/axis_aligner.py (or equivalent)
Signature: execute_axis_aligner_plan(tensor: torch.Tensor, plan: AxisAlignerPlan, side: str) -> torch.Tensor
Description: Execute an axis alignment plan on a tensor. Extended to support einops flatten patterns (e.g. 't nh hd -> t (nh hd)'). Raises ValueError with a message matching 'side must be' when side is not a valid side identifier ('x' or 'y').

Type: Function
Name: compute_unsharder_plan
Location: sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py (or equivalent)
Signature: compute_unsharder_plan(dim_specs: list[DimSpec], parallel_infos: list[dict]) -> list
Description: Compute unsharding plans for a list of dimension specs. Extended to handle fused dims:
  - A fused dim with a sharding modifier (e.g. '[tp]') produces a ConcatParams plan with dim_name equal to the fused dim's sanitized_name (e.g. 'num_heads___head_dim').
  - A fused dim with a partial-reduction modifier (e.g. '[tp:partial]') produces a ReduceSumParams plan.
  - A fused dim without any modifier is treated as replicated and produces a PickParams plan.

Type: Function
Name: execute_unsharder_plan
Location: sglang/srt/debug_utils/comparator/aligner/unsharder/executor.py (or equivalent)
Signature: execute_unsharder_plan(plan, tensors: list[torch.Tensor]) -> UnsharderResult
Description: Execute an unsharding plan. Must use DimSpec.sanitized_name (not DimSpec.name) when assigning named-tensor dimension labels to the input tensors before concatenation or reduction.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.