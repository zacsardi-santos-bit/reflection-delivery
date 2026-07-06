Simplify the covariates extraction workflow in the graphrag pipeline by consolidating the existing subworkflows into a single verb called `create_final_covariates`. Refactor the Covariate model to remove unused fields and ensure the output meets the specified format.

*   Implement the `create_final_covariates` function in `graphrag/index/workflows/v1/subflows/create_final_covariates.py` with the following signature:
    *   `async def create_final_covariates(input: VerbInput, cache: PipelineCache, callbacks: VerbCallbacks, column: str, covariate_type: str, strategy: dict[str, Any] | None, async_mode: AsyncType = AsyncIO, entity_types: list[str] | None = None, **kwargs: dict) -> VerbResult`
    *   Consolidate extraction, ID generation, column renaming, and selection into a single step.
*   Ensure the `create_final_covariates` verb accepts input from the `create_base_text_units` workflow.
*   Update the `Covariate` dataclass in `graphrag/index/verbs/covariates/typing.py`:
    *   Remove `subject_type`, `object_type`, and `doc_id` fields.
*   Ensure the covariates output includes the following columns:
    *   `id` with UUID values.
    *   `human_readable_id` with incrementing string values starting at "1".
    *   `text_unit_id` renamed from `chunk_id`.
    *   `document_ids` and `n_tokens` copied from input text units.
    *   `covariate_type` with value "claim".
    *   `subject_id`, `object_id`, `type`, `status`, `start_date`, `end_date`, `description`, and `source_text`.
*   Implement the `extract_covariates_df` function in `graphrag/index/verbs/covariates/extract_covariates/extract_covariates.py`:
    *   Signature: `async def extract_covariates_df(input: pd.DataFrame, cache: PipelineCache, callbacks: VerbCallbacks, column: str, covariate_type: str, strategy: dict[str, Any] | None, async_mode: AsyncType = AsyncIO, entity_types: list[str] | None = None, **kwargs) -> pd.DataFrame`
    *   Extract covariates and return a DataFrame with the extracted covariates.
*   Ensure the `ClaimExtractor` parses claim tuples in the format: `(SUBJECT<|>OBJECT<|>TYPE<|>STATUS<|>START_DATE<|>END_DATE<|>DESCRIPTION<|>SOURCE_TEXT)`.
*   Ensure the `create_row_from_claim_data` function merges input row data with covariate data and adds `covariate_type`.
*   Include the covariate workflow in the pipeline when `claim_extraction.enabled` is true.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.