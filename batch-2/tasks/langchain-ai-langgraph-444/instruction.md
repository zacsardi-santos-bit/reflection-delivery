Implement a search capability for checkpoint backends in LangGraph to filter checkpoints by metadata and add a score field to checkpoint metadata. Additionally, create a managed value type to automatically retrieve high-quality checkpoints as few-shot examples for graph runs.

*   Update `CheckpointMetadata` in `langgraph/checkpoint/base.py`:
    *   Add an optional `score` field of type `Optional[int]`.

*   Implement metadata search functionality:
    *   In `langgraph/checkpoint/sqlite.py`, create `_metadata_predicate(metadata_filter: CheckpointMetadata) -> Tuple[str, Tuple[Any, ...]]`:
        *   Return a SQL WHERE clause predicate and parameter tuple for filtering checkpoints by metadata.
        *   Use `json_extract(CAST(metadata AS TEXT), '$.KEY') OPERATOR ?` for each key.
        *   Serialize dict and list values to compact JSON strings.
    *   Implement `search_where(metadata_filter: CheckpointMetadata, before: Optional[RunnableConfig] = None) -> Tuple[str, Tuple[Any, ...]]`:
        *   Combine metadata predicates with an optional `thread_ts < ?` bound.
        *   Return appropriate WHERE clause based on provided metadata and `before` parameters.

*   Extend checkpoint storage backends:
    *   In `langgraph/checkpoint/sqlite.py`, update `SqliteSaver`:
        *   Implement `search(metadata_filter, *, before=None, limit=None) -> Iterator[CheckpointTuple]` to yield checkpoints matching the filter.
    *   In `langgraph/checkpoint/memory.py`, update `MemorySaver`:
        *   Implement `search(metadata_filter, *, before=None, limit=None) -> Iterator[CheckpointTuple]`.
        *   Implement `asearch(metadata_filter, *, before=None, limit=None) -> AsyncIterator[CheckpointTuple]`.
    *   In `langgraph/checkpoint/aiosqlite.py`, update `AsyncSqliteSaver`:
        *   Implement `asearch(metadata_filter, *, before=None, limit=None) -> AsyncIterator[CheckpointTuple]`.

*   Create a managed value type for few-shot examples:
    *   In `langgraph/managed/few_shot.py`, define `FewShotExamples` as a generic class extending `ManagedValue`.
        *   Implement `configure(k: int = 5, metadata_filter: dict = None) -> ConfiguredManagedValue`.
        *   Ensure it retrieves checkpoints with `score=1` and populates examples during graph runs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.