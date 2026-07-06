Remove the unsupported 'executor' field from task instance data structures, serialized DAGs, and web UI task views. Ensure that this field is not present in any public-facing data structures to prevent user confusion.

*   Update the TaskInstance model:
    *   Ensure the dictionary representation of a TaskInstance does not include the 'executor' field.
*   Modify the DAG serialization process:
    *   Ensure the serialized form of a DAG's task/operator attributes does not include the 'executor' field.
*   Adjust the web UI task views:
    *   Ensure the web views endpoint returning task instance data does not include the 'executor' field in any task instance record.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.