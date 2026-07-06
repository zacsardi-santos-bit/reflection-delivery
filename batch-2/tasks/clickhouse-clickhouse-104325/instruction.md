One of ClickHouse's integration tests checks that a node correctly cleans up incomplete snapshot transfers to remote object storage after restarting.

*   In tests/integration/test_keeper_snapshot_chunked_transfer/test.py, in the test_recover_after_interrupted_transfer function, replace the set that records interrupted-transfer temporary objects using only the object name with a set that records each object as a tuple of its name and last-modification timestamp, so that each recorded entry uniquely identifies a specific object written at a specific point in time.

*   In tests/integration/test_keeper_snapshot_chunked_transfer/test.py, in the same function's polling loop, update the filter condition that checks for lingering interrupted-transfer objects to match against the tuple of object name and last-modification timestamp rather than against the object name alone, so that a new in-flight object with a coincidentally matching name is not mistaken for the original interrupted-transfer object.


*   Interface details: Type: File
Name: test.py
Location: tests/integration/test_keeper_snapshot_chunked_transfer/test.py
Description: Python integration test file for ClickHouse's keeper snapshot chunked transfer feature. The function test_recover_after_interrupted_transfer must be modified in the remote-storage code path. Specifically, the set that records which temporary bucket objects were left behind by the interrupted transfer must be changed to store (object_name, last_modified) tuples instead of object names alone. The polling loop within the same function that waits for those objects to be cleaned up must also be updated to filter objects using the same tuple-based comparison, so that a newly created object with the same name but a different modification timestamp is excluded from the match. The parametrized test variants that exercise the remote-disk path rely on this function.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.