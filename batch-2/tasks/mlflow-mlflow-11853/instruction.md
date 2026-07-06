Implement the `search_traces` method in the `mlflow/store/tracking/sqlalchemy_store.py` to enable querying and filtering of traces across multiple experiments. Ensure the method supports filtering, sorting, and pagination as specified.

*   Implement `search_traces` with the following signature:
    *   `search_traces(experiment_ids: List[str], filter_string: Optional[str] = None, max_results: int = SEARCH_TRACES_DEFAULT_MAX_RESULTS, order_by: Optional[List[str]] = None, page_token: Optional[str] = None) -> Tuple[List[TraceInfo], Optional[str]]`
    *   Return a tuple containing a list of `TraceInfo` objects and an optional next-page token string.

*   Ensure the method supports:
    *   Filtering on attributes: 'name', 'status', 'timestamp', 'execution_time', 'tags.<tagname>', and 'run_id'.
    *   Combining multiple filter conditions with AND.
    *   Ordering by fields: 'timestamp', 'execution_time', 'experiment_id', 'status', 'name', 'run_id', and 'tag.<tagname>'.
        *   Support 'ASC' or 'DESC' modifiers for ordering.
        *   Default to 'timestamp DESC' when `order_by` is empty or None.
        *   Secondary tie-breaking order by `request_id` ascending when timestamps are identical.
        *   Traces without a specified tag should sort after those with the tag.
    *   Pagination using a `page_token` to retrieve subsequent pages.
        *   Return `None` as the token when no further results are available.

*   Handle invalid `max_results` values:
    *   Raise `MlflowException` with the message 'Invalid value for request parameter' if `max_results` exceeds 50000 or is negative.

*   Implement `_generate_trace_request_id` as a standalone method:
    *   `def _generate_trace_request_id() -> str`
    *   Ensure it returns a unique string ID for each trace.
    *   Delegate request-ID generation in `start_trace` to this method.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.