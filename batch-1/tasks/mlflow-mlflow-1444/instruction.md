Implement pagination support for MLflow's run search functionality by modifying the existing API to return results in manageable chunks. Ensure the result behaves like a list and includes a pagination token for retrieving subsequent pages.

*   Create a `PagedList` class in `mlflow/store/abstract_store.py`:
    *   Subclass the built-in list.
    *   Accept `items` (list) and `token` (string or None) in the constructor.
    *   Initialize the list with `items` and expose `token` via a `.token` attribute.
    *   Ensure items are accessible by index.

*   Update the `AbstractStore` class:
    *   Implement a non-abstract `search_runs` method with signature:
        ```python
        search_runs(self, experiment_ids, filter_string, run_view_type, max_results=SEARCH_MAX_RESULTS_DEFAULT, order_by=None, page_token=None) -> PagedList
        ```
    *   Call `_search_runs` with parameters: `experiment_ids`, `filter_string`, `run_view_type`, `max_results`, `order_by`, `page_token`.
    *   Return `PagedList(runs, token)` from the resulting tuple.

*   Define the abstract `_search_runs` method in `AbstractStore`:
    *   Signature: 
        ```python
        _search_runs(self, experiment_ids, filter_string, run_view_type, max_results, order_by, page_token)
        ```
    *   Return a 2-tuple `(runs, token)`.

*   Implement `_search_runs` in `RestStore`:
    *   Include `page_token` in the `SearchRuns` protobuf message to the `runs/search` endpoint.
    *   Return a 2-tuple `(runs, response_proto.next_page_token)`.

*   Implement `_search_runs` in `SqlAlchemyStore`:
    *   Raise `MlflowException` if `page_token` is a non-empty string.
    *   Return `(runs, None)` when `page_token` is empty or None.

*   Update `MlflowClient`:
    *   Modify `search_runs` method to accept `page_token=None` and pass it to the store's `search_runs`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.