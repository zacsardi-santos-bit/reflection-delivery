I'm working on the experiment page sidebar navigation in MLflow and I've noticed that time range filter parameters get dropped when switching between certain tabs.

*   When navigating between traces-related tabs (such as Overview, Sessions, Traces, and Chat Sessions), the sidebar navigation links must preserve ALL current URL query parameters in their href, including both time range parameters (e.g. startTimeLabel, startTime) and traces-specific parameters (e.g. selectedTraceId).

*   When navigating from a traces-related tab to a non-traces-related tab (such as Datasets), the navigation link href must include time range parameters (e.g. startTimeLabel) from the current URL but must NOT include traces-specific parameters (e.g. selectedTraceId).

*   When navigating from a non-traces-related tab to a traces-related tab (such as Traces), the navigation link href must include time range parameters (e.g. startTimeLabel) from the current URL but must NOT include tab-specific parameters (e.g. selectedDatasetId).

*   When navigating between non-traces-related tabs (e.g. from Datasets to Judges), the navigation link href must include time range parameters (e.g. startTimeLabel) from the current URL but must NOT include any other tab-specific parameters (e.g. selectedDatasetId, viewMode).

*   Time range parameters that qualify for cross-tab preservation include at minimum startTimeLabel and startTime; all other non-time-range parameters are considered tab-specific and must be dropped when navigating outside their applicable tab group.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.