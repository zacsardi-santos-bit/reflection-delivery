I'm working with the issue tracking functionality and I'd like to be able to see how many traces are linked to each issue when I search for issues.

*   The Issue entity must have an optional trace_count field (integer or None). When search_issues is called without include_trace_count=True, this field must be None on all returned Issue objects.

*   The search_issues method in the SQLAlchemy store must accept an optional include_trace_count parameter (boolean, default False). When False, Issue objects are returned with trace_count=None. When True, each returned Issue must have trace_count populated as an integer reflecting the number of traces linked to that issue via IssueReference assessments.

*   When search_issues is called with include_trace_count=True, issues that have no linked traces must have trace_count=0 (not None).

*   When search_issues is called with include_trace_count=True, the results must be sorted first by severity (higher severity first, e.g. HIGH before MEDIUM), and within the same severity level, sorted by trace_count descending.

*   When search_issues is called with include_trace_count=True, pagination (max_results and page_token) must work correctly and each page's issues must have trace_count populated.

*   When search_issues is called with both include_trace_count=True and a filter_string, the filter must apply normally and matched issues must still have trace_count populated.

*   The search_issues method in the RestStore must accept an optional include_trace_count parameter (boolean, default False). The value must always be included in the JSON body sent to the POST issues/search API endpoint, regardless of whether it is True or False.

*   The _search_issues HTTP handler must read the include_trace_count field from the SearchIssues request message and pass it as a keyword argument to the store's search_issues call. The JSON response body must include a trace_count field in each issue object.


*   Interface details: Type: Method
Name: search_issues
Location: mlflow/store/tracking/sqlalchemy_store.py
Signature: search_issues(self, experiment_id: str, filter_string: str = "", max_results: int = ..., page_token: Optional[str] = None, include_trace_count: bool = False) -> PagedList[Issue]
Description: Searches issues for a given experiment. When include_trace_count=False (default), returned Issue objects have trace_count=None. When include_trace_count=True, each Issue has trace_count set to the integer number of traces linked to it via IssueReference assessments. When include_trace_count=True, results are sorted first by severity descending, then by trace_count descending within the same severity.

Type: Method
Name: search_issues
Location: mlflow/store/tracking/rest_store.py
Signature: search_issues(self, experiment_id: str, filter_string: str = "", max_results: int = ..., page_token: Optional[str] = None, include_trace_count: bool = False) -> PagedList[Issue]
Description: Calls the issues/search POST endpoint with a JSON body that always includes the include_trace_count field. Returns a PagedList of Issue objects with trace_count populated if the server returns it.

Type: Function
Name: _search_issues
Location: mlflow/server/handlers.py
Signature: _search_issues()
Description: HTTP handler for the issues/search endpoint. Reads include_trace_count from the SearchIssues request message and forwards it to the store's search_issues call as a keyword argument. The JSON response must include a trace_count field in each issue object.

Type: Class/Field
Name: trace_count
Location: mlflow/entities/issue.py (or equivalent Issue entity definition)
Signature: trace_count: Optional[int] = None
Description: An optional integer field on the Issue entity representing the number of traces linked to the issue. Must be None when not explicitly computed (i.e., when search_issues is called without include_trace_count=True), and must be 0 or a positive integer when computed.

Type: Field
Name: include_trace_count
Location: SearchIssues proto/message definition (mlflow/protos/ or equivalent)
Signature: include_trace_count: bool (default False)
Description: A boolean field on the SearchIssues request message. When True, the server populates trace_count on each returned Issue.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.