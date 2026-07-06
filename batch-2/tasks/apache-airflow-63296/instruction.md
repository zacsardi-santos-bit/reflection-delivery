I'm working on hardening Airflow against path traversal attacks.

*   The validate_key function in airflow/utils/helpers.py must raise AirflowException when the key contains consecutive dots ('..'), with the exact error message format: "The key '{key}' must not contain consecutive dots ('..') to prevent path traversal" (e.g., for key 'my..key': "The key 'my..key' must not contain consecutive dots ('..') to prevent path traversal").

*   The validate_key path traversal check must apply to keys that are otherwise structurally valid (alphanumeric, dashes, dots, underscores) but contain consecutive dots, such as 'my..key' and '..'.

*   Creating a DAG run with a run_id containing '..' (consecutive dots) must raise ValueError with a message that contains the substring "must not contain '..'".

*   The run_id path traversal rejection must apply to all forms of run_ids containing '..', including URL-encoded variants (e.g., 'manual__..%2F..%2Fetc%2Fpasswd'), plain consecutive dots (e.g., 'my..run'), and run_ids that are just '..'.


*   Interface details: Type: Function
Name: validate_key
Location: airflow-core/src/airflow/utils/helpers.py
Signature: validate_key(k: str, max_length: int = 250) -> None
Description: Validates that a key string is acceptable for use as a task key or XCom key. Must raise AirflowException if the key contains consecutive dots ('..'), with the exact message format where the key value appears wrapped in single quotes. For example, for the key value "my..key" the message must be exactly: "The key 'my..key' must not contain consecutive dots ('..') to prevent path traversal". This check must be added in addition to (not instead of) existing checks for type, length, and character set.

Type: Method
Name: validate_run_id
Location: airflow-core/src/airflow/models/dagrun.py
Signature: validate_run_id(self, key: str, run_id: str) -> str | None
Description: SQLAlchemy column validator on the DagRun model that validates the run_id field. Must raise ValueError when the run_id contains consecutive dots ('..'), with a message containing the substring "must not contain '..'". This check must occur before the existing regex and pattern checks.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.