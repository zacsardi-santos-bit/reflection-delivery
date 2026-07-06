Update the snapshot file to ensure it conforms to the current format by removing outdated metadata. This will allow the guard test to pass and enable the full test suite to run without interruption.

*   Modify the snapshot file located at 'crates/pixi_manifest/src/pypi/snapshots/pixi_manifest__pypi__pypi_requirement__tests__deserialize_failing.snap':
    *   Remove the header field 'snapshot_kind: text' from the file.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.