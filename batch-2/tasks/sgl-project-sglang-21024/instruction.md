I'm working on a tensor comparison debug utility for distributed model inference, and I'm running into validation errors whenever I use data parallelism alongside other parallelism strategies like tensor parallelism or context parallelism.

*   The ParallelAxis enum in python/sglang/srt/debug_utils/comparator/dims_spec/types.py must add the following new members: DP with value "dp", ETP with value "etp", EDP with value "edp", ATTN_TP with value "attn_tp", ATTN_DP with value "attn_dp", MOE_EP with value "moe_ep", MOE_TP with value "moe_tp", and MOE_DP with value "moe_dp".

*   A new function _is_dependent_axis must be added to python/sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py. It accepts parallel_infos as a positional argument and parent and child as keyword-only ParallelAxis arguments, and returns True if the child axis rank is uniquely determined by the parent axis rank across all parallel_infos entries where both axes are present. It returns True vacuously when parallel_infos is empty, when the parent axis is absent from all entries, when the child axis is absent from all entries, or when there is only a single entry. It returns False if the same parent rank maps to two different child ranks.

*   A new function _compute_dependent_axes must be added to python/sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py. It accepts parent_axes (a set or frozenset of ParallelAxis), candidate_axes (a set of ParallelAxis), and parallel_infos, and returns a frozenset of candidate axes that are dependent on at least one axis in parent_axes (as determined by _is_dependent_axis). Returns an empty frozenset if no candidates qualify.

*   The existing _validate_explicit_replicated function in python/sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py must accept two new parameters: parallel_infos (list of dicts mapping ParallelAxis to AxisInfo) and dp_filtered_axis (Optional[ParallelAxis], defaulting to None). It must raise ValueError with a message matching the regex 'not found in parallel_infos' when a declared replicated axis is absent from all_axes. It must raise ValueError matching 'both sharded and replicated' when the same axis is in both sharded_axes and explicit_replicated_axes. It must raise ValueError matching '<axis.value>.*not declared' for each active axis that is not declared as sharded, not declared as replicated, not implicitly handled as dependent on a declared parent, and not equal to dp_filtered_axis.

*   Axes that are dependent on any declared (sharded or explicitly replicated) parent axis must be treated as implicitly handled and must not trigger the 'not declared' ValueError in _validate_explicit_replicated. An axis is considered dependent if _is_dependent_axis returns True for it with respect to any declared parent. This applies to both implicitly replicated children (parents are replicated) and implicitly sharded children (parents are sharded).

*   The dp_filtered_axis parameter of _validate_explicit_replicated must exempt exactly that one axis from the undeclared check. It must not exempt any other axis. When dp_filtered_axis is None (default), no special exemption is applied.

*   The compute_unsharder_plan function in python/sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py must accept a new optional keyword argument dp_filtered_axis (Optional[ParallelAxis], defaulting to None) and pass it through to _validate_explicit_replicated. When dp_filtered_axis is set and that axis is present in parallel_infos, no validation error is raised for it and no plan is produced for it. When dp_filtered_axis is set but the axis is absent from all parallel_infos entries, the function behaves as if dp_filtered_axis were None with respect to plan generation.

*   The compute_per_step_sub_plans function in the entrypoint planner must automatically derive a dp_filtered_axis value and pass it to compute_unsharder_plan. The dp_filtered_axis is determined from the dims_spec's dp_group_alias field: if dp_group_alias is set, use ParallelAxis(dp_group_alias); otherwise default to ParallelAxis.DP. This ensures that when DP parallel info (including aliases like moe_dp) is present in the metas, no spurious undeclared validation error is raised, and only the actually-sharded axes (such as TP and CP) produce unsharder plans.


*   Interface details: Type: Enum
Name: ParallelAxis
Location: python/sglang/srt/debug_utils/comparator/dims_spec/types.py
Description: Enumeration of parallel axes. Must add the following new members to the existing TP, CP, EP, SP, RECOMPUTE_PSEUDO values: DP = "dp", ETP = "etp", EDP = "edp", ATTN_TP = "attn_tp", ATTN_DP = "attn_dp", MOE_EP = "moe_ep", MOE_TP = "moe_tp", MOE_DP = "moe_dp".

Type: Function
Name: _is_dependent_axis
Location: python/sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py
Signature: _is_dependent_axis(parallel_infos: list[dict[ParallelAxis, AxisInfo]], *, parent: ParallelAxis, child: ParallelAxis) -> bool
Description: Returns True if the child axis's rank is uniquely determined by the parent axis's rank across all parallel_infos entries where both axes are present. Returns True vacuously when parallel_infos is empty, when no entry contains both parent and child, or when there is only one entry. Returns False when the same parent rank maps to two or more different child ranks in different entries. Note: parent and child are keyword-only arguments.

Type: Function
Name: _compute_dependent_axes
Location: python/sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py
Signature: _compute_dependent_axes(parent_axes: set[ParallelAxis] | frozenset[ParallelAxis], candidate_axes: set[ParallelAxis], parallel_infos: list[dict[ParallelAxis, AxisInfo]]) -> frozenset[ParallelAxis]
Description: Returns a frozenset of axes from candidate_axes that are dependent on at least one axis in parent_axes (as determined by _is_dependent_axis). Returns an empty frozenset if no candidates qualify. All three parameters are passed as keyword arguments by the tests.

Type: Function
Name: _validate_explicit_replicated
Location: python/sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py
Signature: _validate_explicit_replicated(explicit_replicated_axes: frozenset[ParallelAxis], sharded_axes: set[ParallelAxis], all_axes: set[ParallelAxis], parallel_infos: list[dict[ParallelAxis, AxisInfo]], dp_filtered_axis: Optional[ParallelAxis] = None) -> None
Description: Validates that all active axes are declared. Raises ValueError with message matching "not found in parallel_infos" if a declared replicated axis is absent from all_axes. Raises ValueError with message matching "both sharded and replicated" if the same axis appears in both sharded_axes and explicit_replicated_axes. Raises ValueError with a message matching the regex "<axis.value>.*not declared" for any active axis that is not sharded, not explicitly replicated, not implicitly handled as dependent on a declared parent, and not equal to dp_filtered_axis. Axes whose rank is uniquely determined by a declared parent (sharded or replicated) are implicitly handled and do not raise errors. The dp_filtered_axis parameter (default None) exempts exactly that one axis from the undeclared check. Note: this function already exists in the codebase; the parallel_infos and dp_filtered_axis parameters are being added.

Type: Function
Name: compute_unsharder_plan
Location: python/sglang/srt/debug_utils/comparator/aligner/unsharder/planner.py
Signature: compute_unsharder_plan(dim_specs, parallel_infos, *, explicit_replicated_axes: frozenset[ParallelAxis] = frozenset(), thd_global_seq_lens: Optional[list[int]] = None, dp_filtered_axis: Optional[ParallelAxis] = None) -> list[UnsharderPlan]
Description: Existing function that must accept a new keyword argument dp_filtered_axis (Optional[ParallelAxis], defaulting to None). This value is passed through to _validate_explicit_replicated. When set, the specified axis is exempt from validation errors and no plan is produced for it. When the axis is absent from parallel_infos, the parameter has no effect.

Type: Function
Name: compute_per_step_sub_plans
Location: python/sglang/srt/debug_utils/comparator/aligner/entrypoint/planner.py
Signature: compute_per_step_sub_plans(metas: list[dict]) -> list[AlignerPerStepSubPlan]
Description: Existing function that must be modified to automatically derive a dp_filtered_axis value and pass it to compute_unsharder_plan. The dp_filtered_axis is determined from the dims_spec's dp_group_alias field: if dp_group_alias is set, use ParallelAxis(dp_group_alias); otherwise default to ParallelAxis.DP. This ensures that when DP parallel info (including aliases such as moe_dp) is present in the metas, no spurious undeclared validation error is raised, and only the actually-sharded axes produce unsharder plans.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.