Implement a validation mechanism for saving neural network layers with static input specifications to ensure compatibility and support a save-load-save workflow. Address the issues of input specification conflicts and the inability to re-save models without inference.

*   Validate input specifications at save time:
    *   Ensure the provided input specifications are compatible with the layer's declared specifications.
    *   Consider specifications compatible if:
        *   They have the same number of specifications.
        *   Each corresponding pair has the same shape rank.
        *   Concrete dimension values match when both are concrete.
        *   Dynamic dimensions (None or negative values) are treated as wildcards.
        *   Dtypes match after normalization.
    *   Raise a `ValueError` if there is a dtype mismatch, shape rank mismatch, or concrete shape value mismatch.

*   Allow saving without an override specification:
    *   Use the layer's declared specifications if no override is provided.
    *   Ensure the save operation succeeds without error if specifications are compatible or not provided.

*   Support save-load-save workflow:
    *   Allow a layer loaded from disk to be re-saved with a compatible input specification without running inference.
    *   Ensure the re-saved model produces numerically equivalent outputs to the original model (maximum absolute difference less than 1e-5).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.