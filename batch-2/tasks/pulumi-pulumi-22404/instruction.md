I found a bug in the program-based resource refresh operation.

*   When a program-based refresh operation (RefreshV2) is run with a set of excluded resources, only the non-excluded resources must be refreshed (i.e., receive updated outputs from the provider). Excluded resources must remain unchanged with their original outputs intact.

*   When a program-based refresh operation (RefreshV2) is run with a set of targeted resources, only the targeted resources must be refreshed (i.e., receive updated outputs from the provider). Non-targeted resources must remain unchanged with their original outputs intact.

*   After a program-based refresh operation with either exclude or target filtering, the total number of resources in the snapshot must remain the same as before the refresh — no resources should be deleted or added.

*   The exclude and target filtering logic in the refresh step generation must be corrected so that the filtering semantics are not inverted. Previously, the exclude logic was backwards: excluded resources were being included in the refresh and non-excluded resources were being skipped.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.