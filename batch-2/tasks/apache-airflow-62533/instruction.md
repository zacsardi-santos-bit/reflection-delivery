I've noticed that the Task Instances list view in Airflow's web UI is showing raw HTML markup in the DAG ID, Task ID, and Run ID columns instead of rendering them as clickable links.

*   The task_instance_link function in airflow/www/utils.py must return a markupsafe.Markup instance, not a plain string, so that Jinja2 renders it as HTML rather than escaping it.

*   The dag_link function in airflow/www/utils.py must return a markupsafe.Markup instance, not a plain string.

*   The dag_run_link function in airflow/www/utils.py must return a markupsafe.Markup instance, not a plain string.

*   All three functions (task_instance_link, dag_link, dag_run_link) must continue to URL-encode special characters in IDs (e.g. '<' and '>' become '%3C' and '%3E') within their returned HTML, and must not include unencoded angle brackets or ampersands from the original ID values.

*   When task_instance_link is called with a map_index value of -1, the returned markup must NOT include 'map_index' in its content; when called with a non-negative map_index, 'map_index' must be present in the returned markup.


*   Interface details: Type: Function
Name: task_instance_link
Location: airflow/www/utils.py
Signature: task_instance_link(attr: dict) -> markupsafe.Markup
Description: Generates an HTML link for a task instance. Accepts a dict with keys dag_id, task_id, map_index, and execution_date. Must return a markupsafe.Markup instance (not a plain string). The returned markup must URL-encode special characters in dag_id and task_id. When map_index is -1, "map_index" must not appear in the output; for non-negative map_index values, "map_index" must appear in the output.

Type: Function
Name: dag_link
Location: airflow/www/utils.py
Signature: dag_link(attr: dict) -> markupsafe.Markup
Description: Generates an HTML link for a DAG. Accepts a dict with keys dag_id and execution_date. Must return a markupsafe.Markup instance (not a plain string). The returned markup must URL-encode special characters in dag_id.

Type: Function
Name: dag_run_link
Location: airflow/www/utils.py
Signature: dag_run_link(attr: dict) -> markupsafe.Markup
Description: Generates an HTML link for a DAG run. Accepts a dict with keys dag_id, run_id, and execution_date. Must return a markupsafe.Markup instance (not a plain string). The returned markup must URL-encode special characters in dag_id and run_id.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.