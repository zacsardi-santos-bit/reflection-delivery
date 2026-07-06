I've noticed that the endpoint for listing backfills doesn't respect user authorization.

*   The GET /backfills endpoint must filter the list of returned backfills based on the requesting user's DAG access permissions, so that only backfills whose associated DAG is authorized for the user are included in the response.

*   When handling a GET /backfills request, the implementation must call BaseAuthManager.get_authorized_dag_ids exactly once with the current user and method='GET' to retrieve the set of DAG IDs the user may access.

*   The response body's total_entries field must reflect only the count of backfills accessible to the user after authorization filtering, not the total count of all backfills in the system.

*   The response body's backfills array must contain only backfills whose dag_id is present in the authorized DAG IDs set returned by the authorization check.

*   The authorization filter adds one additional database query per request, increasing the query count by 1 compared to the pre-authorization baseline (from 2 to 3 when no dag_id filter is applied; from 3 to 4 when a dag_id filter parameter is provided).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.