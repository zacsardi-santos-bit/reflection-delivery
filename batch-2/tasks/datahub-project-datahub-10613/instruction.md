Update the DataHub Airflow integration plugin to emit detailed identity metadata for datasets used as task inputs or outputs. Replace the current existence signal with a 'datasetKey' aspect containing the dataset's platform, name, and environment.

*   Emit a 'datasetKey' aspect instead of a 'status' aspect for datasets appearing as task inlets or outlets.
    *   Include the following fields in the 'datasetKey' aspect:
        *   'platform': The platform URN (e.g., 'urn:li:dataPlatform:snowflake').
        *   'name': The dataset's name (e.g., 'mydb.schema.tableA').
        *   'origin': The environment or origin (e.g., 'PROD' or 'DEV').
*   Ensure this behavior is consistent in both standard lineage reporting and task execution capture modes.
*   Apply these changes to all datasets, including those referenced by direct URN, for both Snowflake and SQLite platforms.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.