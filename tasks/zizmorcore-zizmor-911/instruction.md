Update the GitHub Actions security scanner to accurately identify and handle environment variables accessed through the expression context. Ensure the scanner distinguishes between static platform-provided variables and potentially dynamic user-defined variables, adjusting the finding and suppression counts accordingly.

*   Implement logic to recognize platform-provided runner environment variables accessed through the expression-language environment context as static.
    *   Suppress findings for these variables or report them at a low severity level.
    *   Include variables such as actor name, reference name, and runner OS in this static category.
*   Ensure the special CI variable is not treated as static, even though it is a runner-provided default, due to its potential for being overridden.
*   Treat any environment variable accessed through the expression context that is not a known platform default as potentially dynamic.
    *   Flag these variables appropriately to prevent incorrect static classification.
*   Implement a case-insensitive check for variable names when determining staticness in the expression context.
*   Validate the implementation with specific audit outputs:
    *   For `static-env.yml`, ensure the output reports exactly 12 total findings with 9 suppressed: 0 unknown, 0 informational, 3 low, 0 medium, 0 high.
    *   For `issue-418-repro.yml`, ensure the output reports no findings with exactly 2 suppressed.
    *   For the `gha-hazmat` repository fixture, ensure the output reports exactly 123 total findings with 28 suppressed: 0 unknown, 6 informational, 0 low, 37 medium, 52 high.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.