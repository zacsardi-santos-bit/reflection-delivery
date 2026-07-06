Implement a Jinja template macro in Superset that allows referencing a named dataset metric directly inside a SQL expression. This macro should accept a metric name and optionally a dataset ID, resolving the metric's SQL expression for use in queries.

*   Create a function `metric_macro` in `superset/jinja_context.py` with the signature `metric_macro(metric_key: str, dataset_id: Optional[int] = None) -> str`.
*   Ensure `metric_macro` can be imported from `superset.jinja_context`.
*   When `metric_macro` is called with a `dataset_id`:
    *   Use `DatasetDAO.find_by_id` to look up the dataset.
    *   Return the metric's SQL expression if the dataset and metric exist.
    *   Raise `DatasetNotFoundError` with 'Dataset ID {dataset_id} not found.' if the dataset is not found.
    *   Raise `SupersetTemplateException` with 'Metric ``{metric_key}`` not found in {table_name}.' if the metric is not found.
*   When `metric_macro` is called without a `dataset_id`:
    *   Call `get_form_data()` from `superset.views.utils` once to resolve the dataset from context.
    *   Use the following resolution order for determining `dataset_id`:
        *   If the second element from `get_form_data()` is a `Slice` with a non-None `datasource_id`, use that `datasource_id`.
        *   If `form_data["url_params"]["datasource_id"]` is non-None, use it as the `dataset_id`.
        *   If `form_data["slice_id"]` is non-None, call `ChartDAO.find_by_id` with that `slice_id` and use the resulting chart's `datasource_id`.
    *   Raise `SupersetTemplateException` with 'Please specify the Dataset ID for the ``{metric_key}`` metric in the Jinja macro.' if no `dataset_id` can be determined.
    *   Handle cases where `ChartDAO.find_by_id` returns None or `datasource_id` is None by raising the same exception.
*   Ensure that when a SQL-type adhoc metric expression contains a Jinja macro referencing a named metric, the compiled SQL query expands the macro to the metric's SQL expression and includes it with the quoted metric label.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.