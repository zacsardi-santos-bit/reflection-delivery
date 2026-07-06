## Description

Currently, Airflow provides no mechanism for operators or users to request that a specific DAG file be parsed immediately or ahead of other files. The DAG processor cycles through all files in a predetermined order (alphabetical, random, or by modification time), and there is no way to influence that queue programmatically. This makes it difficult to get fast feedback after modifying a specific DAG, since you have to wait for the processor to naturally reach that file in its cycle.

## Expected Behavior

- There should be a REST API endpoint that allows authorized users to submit a request for a specific DAG file to be re-parsed with higher priority.
- When such a request is received for a file that contains registered DAGs and the user has appropriate edit permissions, the server should accept it (returning a success status) and record the request.
- If the file is not recognized as a DAG file, the request should be rejected with a "not found" response.
- If the requesting user does not have the necessary permissions, the request should be rejected with a "forbidden" response.
- Submitting the same file path multiple times should not cause errors — the system should handle duplicates gracefully and still return success.
- The DAG file processor should check for pending priority requests during each processing loop and move those files to the front of its queue so they are processed before others.
- Priority parsing records should be cleaned up automatically after each processing loop iteration, so the table does not accumulate stale data. This table should also be excluded from the general database maintenance cleanup utility, since it manages its own lifecycle.

## Why This Matters

Operators managing active Airflow environments need the ability to quickly validate changes to individual DAGs without waiting through a full processing cycle. This feature provides that control via the existing REST API, making the DAG parsing lifecycle more responsive and operator-friendly.
