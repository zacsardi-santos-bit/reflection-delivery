Implement functionality to ensure correct restoration of Flink's compressed operator state checkpoints. Address issues with byte offsets in shared compressed streams by ensuring proper flushing and seeking behavior.

*   Update `CompressibleFSDataOutputStream`:
    *   Accept `FSDataOutputStream` as the delegate parameter in the constructor.
    *   Implement `getPos()` to flush the compression stream before returning the position of the underlying delegate stream. This ensures the position is a valid seek target.

*   Update `CompressibleFSDataInputStream`:
    *   Implement `seek(long desired)` to discard any buffered decompressed bytes before seeking to the desired position in the underlying raw stream. Ensure subsequent reads return data from the new position.
    *   Ensure that after a partial read, any remaining buffered bytes are discarded when seeking to a new position, maintaining correct data reads.

*   Ensure operator state snapshot and restore functionality:
    *   Verify that operator state written with `CompressibleFSDataOutputStream` can be correctly restored using `CompressibleFSDataInputStream.seek()`, both with and without snapshot compression.
    *   Handle multiple `OperatorStateHandle` objects by concatenating their list state values in the restored state.
    *   Preserve empty list and broadcast states through snapshot and restore without errors.
    *   After repartitioning operator state using a round-robin strategy, ensure each partition contains the correct proportional subset of original list state values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.