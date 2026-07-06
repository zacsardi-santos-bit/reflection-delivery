I'm working on a tensor comparison tool for debugging model outputs across multiple forward passes.

*   All existing token-aligner modules (types, aux_loader, aux_plugins, executor, planner, seq_info_builder) must be importable from a new 'smart' sub-package at sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.* (e.g. sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.types, etc.).

*   A new function execute_token_aligner_concat_steps must exist at sglang.srt.debug_utils.comparator.aligner.token_aligner.concat_steps. It must accept a single keyword argument tensor_of_step_pair of type Pair[Dict[int, torch.Tensor]] and return a Pair[torch.Tensor]. Steps are concatenated in ascending step-key order.

*   execute_token_aligner_concat_steps must truncate both sides to the minimum size along the token dimension after concatenation. When sides have different total lengths, the longer side is trimmed to match the shorter side.

*   execute_token_aligner_concat_steps must detect the token dimension from named tensor dimensions: dimension named 't' (token) takes priority; if absent, dimension named 's' (sequence) is used; if neither is present, dim 0 is used as the fallback.

*   A new function load_thd_seq_lens_only must exist at sglang.srt.debug_utils.comparator.aligner.token_aligner.concat_steps.thd_seq_lens_loader. It must accept keyword arguments dump_path (Path) and df (polars DataFrame), and return dict[int, list[int]] or None.

*   load_thd_seq_lens_only must return None when no recognized framework plugin is detected from the provided dump metadata.

*   load_thd_seq_lens_only must return None when a plugin is detected but it reports no CP-sharded names (empty frozenset).

*   load_thd_seq_lens_only must return None when the plugin has CP-sharded names but neither a seq_lens tensor nor a cu_seqlens_q tensor is present in the dump.

*   load_thd_seq_lens_only must extract per-step sequence lengths for SGLang format from a tensor named 'seq_lens', returning {step: [len1, len2, ...]}. For Megatron format it derives lengths from 'cu_seqlens_q' via consecutive differences (e.g. cumulative sums [0, 3, 8] yield [3, 5]).

*   load_thd_seq_lens_only must return results for all steps present in the dump as a dict keyed by integer step index.

*   The thd_seq_lens_loader module must import _detect_plugin as a module-level name (imported from sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.aux_loader), so that the name '_detect_plugin' is accessible at sglang.srt.debug_utils.comparator.aligner.token_aligner.concat_steps.thd_seq_lens_loader._detect_plugin for test patching.

*   The compute_aligner_plan function must accept a new keyword argument token_aligner_mode (Optional[str]) in addition to its existing parameters. The returned AlignerPlan object must expose a token_aligner_mode attribute whose value equals the argument passed in.

*   The AlignerPlan type must include a token_aligner_mode attribute (Optional[str]) alongside the existing token_aligner_plan attribute.

*   The comparator entrypoint pipeline must accept a token_aligner argument (string) on the Namespace. The default value must be 'concat_steps'. When token_aligner='concat_steps', multi-step tensors for a single logical tensor name are concatenated into one comparison result instead of producing one result per step.

*   In concat mode, the ComparisonRecord's aligner_plan field must have token_aligner_mode == 'concat_steps' and token_aligner_plan == None.

*   In concat mode, auxiliary tensor names (such as input_ids and positions) must NOT be filtered out from the comparison set — all tensor names present in the dump participate in comparison.

*   In concat mode, the comparator must still accept token_aligner='smart' to invoke the existing sequence-aware alignment logic.


*   Interface details: Type: Function
Name: execute_token_aligner_concat_steps
Location: sglang/srt/debug_utils/comparator/aligner/token_aligner/concat_steps/__init__.py (or concat_steps.py)
Signature: execute_token_aligner_concat_steps(tensor_of_step_pair: Pair[Dict[int, torch.Tensor]]) -> Pair[torch.Tensor]
Description: Concatenates per-step tensors from both sides of a comparison into a single tensor each. Steps are concatenated in ascending step-key order. Detects the token dimension from named dims ('t' first, then 's', then dim 0 fallback). Truncates both sides to the minimum total size along the token dimension.

Type: Function
Name: load_thd_seq_lens_only
Location: sglang/srt/debug_utils/comparator/aligner/token_aligner/concat_steps/thd_seq_lens_loader.py
Signature: load_thd_seq_lens_only(dump_path: Path, df: pl.DataFrame) -> dict[int, list[int]] | None
Description: Loads per-step sequence lengths from tensor dump files without running the full alignment pipeline. Returns a dict mapping step index (int) to a list of per-sequence lengths (list[int]). Returns None if no recognized plugin is detected, if the plugin has no CP-sharded names (empty frozenset), or if neither a seq_lens (SGLang) nor a cu_seqlens_q (Megatron) tensor is present. For Megatron format, lengths are derived via consecutive differences of the cu_seqlens_q cumulative tensor. This module must import _detect_plugin at the module level (from sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.aux_loader) so that _detect_plugin is a patchable module-level name in sglang.srt.debug_utils.comparator.aligner.token_aligner.concat_steps.thd_seq_lens_loader.

Type: Module
Name: smart (sub-package)
Location: sglang/srt/debug_utils/comparator/aligner/token_aligner/smart/
Description: The existing token-aligner modules must be importable from this sub-package. Specifically, these paths must work:
  - sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.types (TokenAlignerPlan, TokenLocator, PositionalSeqId, SGLangSeqId, SeqId, TokenAlignerGlobalAux, TokenAlignerStepAux, TokenAlignerSeqInfo)
  - sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.aux_loader (_detect_plugin, _ensure_dims_in_metas, _load_and_align_aux_tensor, _load_non_tensor_aux, warning_sink)
  - sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.aux_plugins (_infer_positions, _MegatronPlugin, _SGLangPlugin)
  - sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.executor (execute_token_aligner)
  - sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.planner (_match_sequences, compute_token_aligner_plan)
  - sglang.srt.debug_utils.comparator.aligner.token_aligner.smart.seq_info_builder (build_seqs_info)

Type: Function
Name: compute_aligner_plan
Location: sglang/srt/debug_utils/comparator/aligner/entrypoint/planner.py
Signature: compute_aligner_plan(metas_pair: Pair, token_aligner_mode: Optional[str], token_aligner_plan: Optional[TokenAlignerPlan]) -> AlignerPlan
Description: Builds an AlignerPlan from per-step metadata. The new token_aligner_mode parameter must be passed through and stored on the returned AlignerPlan as its token_aligner_mode attribute.

Type: Class
Name: AlignerPlan
Location: sglang/srt/debug_utils/comparator/aligner/entrypoint/types.py
Description: Data class / model representing the full alignment plan. Must include a token_aligner_mode attribute (Optional[str]) alongside the existing token_aligner_plan attribute.
Signature: token_aligner_mode: Optional[str]  (new field; equals the value passed to compute_aligner_plan)

Type: Namespace field
Name: token_aligner
Location: Comparator entrypoint argument parser (e.g. sglang/srt/debug_utils/comparator/entrypoint.py or equivalent)
Description: String argument added to the CLI/Namespace for selecting the token alignment mode. Must accept at least "concat_steps" and "smart". The default value used in the entrypoint pipeline is "concat_steps". When set to "concat_steps", multi-step tensors are concatenated into a single comparison, and the resulting ComparisonRecord's aligner_plan has token_aligner_mode == "concat_steps" and token_aligner_plan == None. In concat mode, auxiliary tensor names are NOT filtered from the dump before comparison (all tensors participate). When set to "smart", the existing sequence-aware alignment logic runs, and auxiliary tensors ARE filtered from the main comparison set.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.