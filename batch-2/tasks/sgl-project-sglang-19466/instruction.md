I'm working on a tensor comparison debugging tool for distributed model runs and I need to add some display capabilities that are currently missing.

*   The AlignerPlan constructor must not accept a token_dims parameter; creating an AlignerPlan without token_dims must succeed.

*   The function _render_polars_as_text(df, *, title=None) must return a string that includes the provided title and all column names of the DataFrame; it must handle empty DataFrames without error.

*   The function _collect_rank_info(df, dump_dir) must return None when no rows in df have name equal to 'input_ids'. When matching rows are found, it must return a list of dicts each containing at least 'rank', and parallel dimension entries formatted as 'rank/size' (e.g., 'tp': '0/2', 'pp': '0/1') by reading sglang_parallel_info from the corresponding .pt file metadata. Entries must be deduplicated so that at most one entry exists per unique rank value.

*   The function _collect_input_ids_and_positions(df, dump_dir, *, tokenizer=None) must return None when no rows in df have name in ['input_ids', 'positions']. When matching rows are found, it must return a list of dicts, one per (step, rank) pair, each containing 'step', 'rank', 'num_tokens' (integer count of tokens), 'input_ids' (string representation), and 'positions' (string representation). When a tokenizer object is provided, each dict must additionally contain 'decoded_text' whose value is the repr() of tokenizer.decode(ids_list, skip_special_tokens=False).

*   The function _extract_parallel_info(row_data, info) must modify row_data in-place. For each key in info that ends with '_rank' and has a corresponding '_size' key, it must set row_data[base] = '{rank}/{size}'. It must do nothing if info is empty or if info contains an 'error' key. It must skip any '_rank' key that does not have a corresponding '_size' key.

*   The class RankInfoRecord in sglang.srt.debug_utils.comparator.output_types must accept constructor arguments label (str) and rows (list of dicts). Its to_text() method must return a string that includes '{label} ranks' as a title and the column names 'rank', 'tp', 'pp' along with their values. Its model_dump_json() method must return a JSON string containing '"type":"rank_info"' and '"label":<label>'.

*   The class InputIdsRecord in sglang.srt.debug_utils.comparator.output_types must accept constructor arguments label (str) and rows (list of dicts). Its to_text() method must return a string that includes '{label} input_ids & positions' as a title and the column names 'step', 'num_tokens', and token values. When rows contain a 'decoded_text' key, to_text() must include 'decoded_text' and its value in the output. Its model_dump_json() method must return a JSON string containing '"type":"input_ids"', '"label":<label>', and '"decoded_text"' when that field is present in the rows.

*   The function read_tokenizer_path(directory: Path) in sglang.srt.debug_utils.dump_loader must scan .pt files in the given directory and return the string value of 'tokenizer_path' from the metadata of the first file that contains it. It must return None if the directory is empty, if no .pt files exist, or if no file's metadata contains 'tokenizer_path'. Files whose metadata does not contain 'tokenizer_path' must be skipped.

*   ComparisonRecord in sglang.srt.debug_utils.comparator.output_types must have an optional aligner_plan field (defaulting to None). When aligner_plan is set, its to_text() method must include 'Aligner Plan:' and the type string of each sub-plan (e.g., 'unsharder'). ComparisonRecord must serialize correctly via model_dump_json() including the aligner_plan and its sub-plan type discriminators.

*   The function parse_record_json(json_str: str) in sglang.srt.debug_utils.comparator.output_types must deserialize a JSON string into the appropriate record type. When the JSON represents a ComparisonRecord with an aligner_plan, the result must have aligner_plan populated with correct sub_plans whose .type attribute matches the discriminator value (e.g., 'unsharder'). When aligner_plan is absent in the JSON, the result must have aligner_plan equal to None.

*   AlignerPerStepPlan in sglang.srt.debug_utils.comparator.aligner.entrypoint.types must accept constructor arguments step (int), input_object_indices (list[int]), and sub_plans (list). Each entry in sub_plans must expose a .type attribute.

*   UnsharderPlan in sglang.srt.debug_utils.comparator.aligner.unsharder.types must have a type field with Literal value 'unsharder' as its default.

*   ConcatParams must be importable from sglang.srt.debug_utils.comparator.aligner.unsharder.types. ParallelAxis must be importable from sglang.srt.debug_utils.comparator.dims.


*   Interface details: Type: Function
Name: _render_polars_as_text
Location: python/sglang/srt/debug_utils/comparator/display.py
Signature: _render_polars_as_text(df: pl.DataFrame, *, title: Optional[str] = None) -> str
Description: Renders a Polars DataFrame as a formatted text table, optionally with a title. Returns a string containing the title (if provided) and all column names and values.

Type: Function
Name: _collect_rank_info
Location: python/sglang/srt/debug_utils/comparator/display.py
Signature: _collect_rank_info(df: pl.DataFrame, dump_dir: Path) -> Optional[list[dict[str, Any]]]
Description: Collects per-rank parallel topology information from dump files. Returns None if no rows named "input_ids" are found. Otherwise returns a deduplicated list of dicts (one per rank), each containing "rank" and any parallel dimension entries (e.g., "tp": "0/2", "pp": "0/1") read from sglang_parallel_info in file metadata.

Type: Function
Name: _collect_input_ids_and_positions
Location: python/sglang/srt/debug_utils/comparator/display.py
Signature: _collect_input_ids_and_positions(df: pl.DataFrame, dump_dir: Path, *, tokenizer: Any = None) -> Optional[list[dict[str, Any]]]
Description: Collects input token IDs and positions from dump files. Returns None if no rows named "input_ids" or "positions" are found. Otherwise returns a list of dicts, one per (step, rank) pair, each with "step", "rank", "num_tokens", "input_ids" (str), "positions" (str). When tokenizer is provided, adds "decoded_text" as repr(tokenizer.decode(ids_list, skip_special_tokens=False)).

Type: Function
Name: _extract_parallel_info
Location: python/sglang/srt/debug_utils/comparator/display.py
Signature: _extract_parallel_info(row_data: dict[str, Any], info: dict[str, Any]) -> None
Description: Extracts parallel dimension entries from a metadata info dict and writes them into row_data in-place. For each key ending in "_rank" with a matching "_size" key, sets row_data[base] = "{rank}/{size}". Does nothing if info is empty or contains an "error" key. Ignores "_rank" keys without a corresponding "_size" key.

Type: Class
Name: RankInfoRecord
Location: python/sglang/srt/debug_utils/comparator/output_types.py
Description: Output record that displays per-rank topology information as a table.
Signature:
  __init__(label: str, rows: list[dict[str, Any]])
  to_text() -> str  # Returns text with title "{label} ranks" and columns "rank", "tp", "pp"
  model_dump_json() -> str  # JSON with "type":"rank_info" and "label"

Type: Class
Name: InputIdsRecord
Location: python/sglang/srt/debug_utils/comparator/output_types.py
Description: Output record that displays input token IDs and positions as a table.
Signature:
  __init__(label: str, rows: list[dict[str, Any]])
  to_text() -> str  # Returns text with title "{label} input_ids & positions", columns "step", "num_tokens", etc.; includes "decoded_text" column when present in rows
  model_dump_json() -> str  # JSON with "type":"input_ids", "label", and "decoded_text" when present

Type: Function
Name: read_tokenizer_path
Location: python/sglang/srt/debug_utils/dump_loader.py
Signature: read_tokenizer_path(directory: Path) -> Optional[str]
Description: Scans .pt files in the given directory and returns the "tokenizer_path" value from the metadata of the first file that contains it. Returns None if the directory is empty, contains no .pt files, or no file has "tokenizer_path" in its metadata.

Type: Function
Name: parse_record_json
Location: python/sglang/srt/debug_utils/comparator/output_types.py
Signature: parse_record_json(json_str: str) -> <output record type>
Description: Parses a JSON string into the appropriate output record type. When parsing a ComparisonRecord with an aligner_plan, correctly deserializes discriminated union sub-plans (e.g., with type="unsharder"). Returns a ComparisonRecord with aligner_plan=None when that field is absent.

Type: Class
Name: ComparisonRecord
Location: python/sglang/srt/debug_utils/comparator/output_types.py
Description: Existing output record class extended with an optional aligner_plan field.
Signature:
  aligner_plan: Optional[AlignerPlan] = None  # New field
  to_text() -> str  # When aligner_plan is set, includes "Aligner Plan:" and sub-plan type strings (e.g., "unsharder")
  model_dump_json() -> str  # Serializes aligner_plan including sub-plan type discriminators

Type: Class
Name: AlignerPlan
Location: python/sglang/srt/debug_utils/comparator/aligner/entrypoint/types.py
Description: Represents the full plan for aligning tensors before comparison. The token_dims parameter has been removed; constructors must work without it.
Signature:
  __init__(per_step_plans: Pair[list[AlignerPerStepPlan]], token_aligner_plan: Optional[TokenAlignerPlan] = None, axis_swapper_plan: Optional[AxisSwapperPlan] = None)

Type: Class
Name: AlignerPerStepPlan
Location: python/sglang/srt/debug_utils/comparator/aligner/entrypoint/types.py
Description: Plan for a single step in the aligner.
Signature:
  __init__(step: int, input_object_indices: list[int], sub_plans: list[AlignerPerStepSubPlan])
  # sub_plans entries expose a .type attribute (e.g., "unsharder", "reorderer")

Type: Class
Name: UnsharderPlan
Location: python/sglang/srt/debug_utils/comparator/aligner/unsharder/types.py
Description: Plan for an unsharding operation. Must have a type field with default value "unsharder".
Signature:
  type: Literal["unsharder"] = "unsharder"

Type: Class
Name: ConcatParams
Location: python/sglang/srt/debug_utils/comparator/aligner/unsharder/types.py
Description: Parameters for a concatenation-based unsharding operation. Must be importable from this module.

Type: Enum/Class
Name: ParallelAxis
Location: python/sglang/srt/debug_utils/comparator/dims.py
Description: Enum or class representing a parallel axis (e.g., TP, PP). Must be importable from this module.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.