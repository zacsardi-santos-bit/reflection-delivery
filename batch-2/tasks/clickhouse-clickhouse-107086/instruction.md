There's an outdated stateless SQL test in the ClickHouse test suite that tests array normalization functions over arrays of an experimental floating-point type.

*   The file tests/queries/0_stateless/04319_array_norm_bfloat16.sql must be deleted from the repository.

*   The file tests/queries/0_stateless/04319_array_norm_bfloat16.reference must be deleted from the repository.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.