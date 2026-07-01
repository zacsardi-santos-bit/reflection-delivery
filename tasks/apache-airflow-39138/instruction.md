Implement a priority DAG file parsing feature in Apache Airflow by creating a REST API endpoint that allows authorized users to request immediate parsing of specific DAG files. Ensure that the system handles duplicate requests gracefully and maintains efficient processing by prioritizing requested files.

*   Define the `DagPriorityParsingRequest` model in `airflow/models/dagbag.py`:
    *   Use SQLAlchemy ORM with a table named 'dag_priority_parsing_request'.
    *   Include a 'fileloc' column (String, max 2000 chars, not null).
    *   Include an 'id' column (String, max 32 chars, primary key) auto-generated as the MD5 hex digest of 'fileloc'.
    *   Ensure the constructor accepts a single argument: `fileloc` (str).

*   Implement a PUT endpoint at `/api/v1/parseDagFile/{file_token}` in `airflow/api_connexion/endpoints/dag_parsing.py`:
    *   Define the function `reparse_dag_file(*, file_token: str, session: Session = NEW_SESSION) -> Response`.
    *   Decode `file_token` to a file path and verify it corresponds to registered DAGs.
    *   Check if the requesting user has edit access to the DAGs in that file.
    *   Return HTTP 201 on success, even for duplicate requests, and create a `DagPriorityParsingRequest` record.
    *   Handle duplicate requests by catching `IntegrityError` and still returning HTTP 201.
    *   Return HTTP 404 if the file token is invalid or no DAGs are found.
    *   Return HTTP 403 if the user lacks the necessary edit permissions.

*   Update `DagFileProcessorManager` in `airflow/dag_processing/manager.py`:
    *   Implement `_refresh_requested_filelocs(self, session=NEW_SESSION) -> None`.
    *   Query all `DagPriorityParsingRequest` records and move their `fileloc` to the front of `_file_path_queue` if present.
    *   Delete each `DagPriorityParsingRequest` record after processing.
    *   Ensure `_file_path_queue` maintains priority-requested files at the front.

*   Exclude 'dag_priority_parsing_request' table from the standard database cleanup configuration to allow the DAG processing loop to manage record lifecycle.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.