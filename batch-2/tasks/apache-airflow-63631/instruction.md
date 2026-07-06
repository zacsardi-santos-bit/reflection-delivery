I'm working on the Airflow DAG calendar API and I've run into a problem with partitioned DAGs.

*   The GET /calendar/{dag_id} endpoint must accept optional datetime query parameters `partition_date_gte`, `partition_date_lte`, `partition_date_gt`, and `partition_date_lt` (ISO8601 format), in addition to the existing `logical_date_*` parameters.

*   When no date filter is active (neither partition_date nor logical_date filters supplied), the endpoint must return all DAG runs and use the effective date for each run: if the run has a partition_date, that value is used as the `date` field in the response; otherwise the run's logical_date is used.

*   When `partition_date_gte` and/or `partition_date_lte` filters are supplied, only runs whose partition_date falls within the specified range are returned. Runs that have no partition_date (i.e. logical_date-only runs) must be excluded from the results.

*   When `logical_date_gte` and/or `logical_date_lte` filters are supplied (with no partition_date filters), only runs whose logical_date falls within the range are returned. Runs that have no logical_date (i.e. partition_date-only runs) must be excluded.

*   The response structure remains unchanged: `{"total_entries": <int>, "dag_runs": [{"date": <ISO8601 UTC string>, "state": <string>, "count": <int>}, ...]}`. Dates in the response must be formatted as UTC ISO8601 strings ending in `Z` (e.g., `2025-01-01T00:00:00Z`).

*   Both `granularity=daily` (default) and `granularity=hourly` must work correctly when processing partitioned DAG runs using partition_date as the effective date.

*   The route function `get_calendar` in `airflow-core/src/airflow/api_fastapi/core_api/routes/ui/calendar.py` must receive the new partition_date range filter (constructed via `datetime_range_filter_factory("partition_date", DagRun)`) and forward it to the calendar service.

*   The `CalendarService` class in `airflow-core/src/airflow/api_fastapi/core_api/services/ui/calendar.py` must accept a `partition_date: RangeFilter` argument in its main time-range calculation method and apply it when the partition_date filter is active (taking precedence over the logical_date filter when a partition_date filter is provided).


*   Interface details: Type: Function
Name: get_calendar
Location: airflow-core/src/airflow/api_fastapi/core_api/routes/ui/calendar.py
Signature: get_calendar(dag_id: Annotated[str, Path(...)], session: SessionDep, dag_bag: DagBagDep, logical_date: Annotated[RangeFilter, Depends(datetime_range_filter_factory("logical_date", DagRun))], partition_date: Annotated[RangeFilter, Depends(datetime_range_filter_factory("partition_date", DagRun))], granularity: Literal["hourly", "daily"] = "daily") -> CalendarTimeRangeCollectionResponse
Description: FastAPI route handler for GET /calendar/{dag_id}. Must be extended with a new `partition_date` parameter built using `datetime_range_filter_factory("partition_date", DagRun)`. The `partition_date` filter is then passed through to the calendar service alongside the existing `logical_date` filter.

Type: Class
Name: CalendarService
Location: airflow-core/src/airflow/api_fastapi/core_api/services/ui/calendar.py
Description: Service class implementing the calendar data logic. Its main public method that computes historical and planned DAG run calendar entries must be updated to accept a `partition_date: RangeFilter` parameter. When the partition_date filter is active (i.e. it has a non-empty value), it takes precedence over the logical_date filter for database queries and date range checks. When neither filter is active, the effective date for each historical run is determined by COALESCE(partition_date, logical_date) — partition_date is used if non-null, otherwise logical_date.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.