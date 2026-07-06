Implement a utility function `analyzeEvalSource` to statically analyze eval test files and extract structured metadata about each test case without executing them. Ensure it handles various patterns such as direct helper calls, import aliases, local wrapper functions, and JSX/TSX syntax.

*   Implement `analyzeEvalSource` with the following signature:
    *   `analyzeEvalSource(source: string, options: { filePath: string; repoRoot?: string }) -> AnalysisResult`
    *   Located in `scripts/utils/eval-analysis.ts`
    *   Return an `AnalysisResult` object containing `relativePath`, `cases`, `helpers`, and `diagnostics`.

*   Ensure the function:
    *   Accepts a source code string and an options object with a `filePath` and optional `repoRoot`.
    *   Returns structured metadata for each test case, including `policy`, `name`, `suiteName`, `suiteType`, `timeout`, `hasFiles`, and `hasPrompt`.
    *   Tracks a map of wrapper and alias names to their base helpers.

*   Handle various source patterns:
    *   Direct eval helper calls with string literal policy and object literal eval case should produce entries in `cases` with `helperName` and `baseHelperName` set to the imported helper name.
    *   Top-level local wrapper functions calling a base helper should map in `helpers` and resolve correctly in `cases`.
    *   Wrapper functions inside describe callbacks and arrow functions in multi-variable declarations should be mapped and resolved correctly.
    *   Outer utility functions containing inner wrapper functions should only map the inner function.
    *   Aliased imports should map aliases to the original imported name in `helpers`.
    *   JSX/TSX syntax should be parsed successfully, extracting cases correctly.

*   Normalize file paths:
    *   Convert all `relativePath` values to use forward slashes, regardless of the host operating system.
    *   If `filePath` is relative and no `repoRoot` is provided, set `relativePath` to the `filePath` with backslashes converted to forward slashes.

*   Handle dynamic values:
    *   If a call uses a non-literal policy or eval case argument, add no entry to `cases` and add a diagnostic message explaining the issue.
    *   Ensure diagnostic messages follow the pattern 'Could not statically resolve policy for <helperName> call.' or 'Could not statically resolve eval case object for <helperName> call.'

*   Ensure all calls in the source are statically resolvable, returning an empty `diagnostics` array when successful.

*   Recognize base eval helper names: 'evalTest', 'appEvalTest', and 'componentEvalTest'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.