Implement enhancements to the error classification system and develop utility functions for normalizing and analyzing Storybook test results. Extend the error classification to recognize additional error patterns and create shared utilities for consistent test result processing.

*   Update the error classification function:
    *   Classify 'too many re-renders', 'maximum update depth exceeded', and errors containing both 'hook' and 'function component' as HOOK_USAGE_ERROR.
    *   Classify 'target container is not a dom element' as MISSING_PORTAL_ROOT.
    *   Classify errors matching 'context not found', 'no provider found for context', 'without a provider', and those with 'context' and 'null' or 'not found' as MISSING_PROVIDER.
    *   Classify errors matching 'is not a function', 'is not an object', 'is not defined', 'element type is invalid', 'objects are not valid as a react child', and 'maximum call stack' as COMPONENT_RENDER_ERROR.

*   Implement utility functions in `code/core/src/shared/utils/to-story-test-result.ts`:
    *   `extractErrorMessage(message: string | undefined, stack: string | undefined): string`
        *   Strip Storybook debug banners from error messages.
        *   Return the first non-empty line of the cleaned message or fall back to the stack trace.
        *   Return 'unknown error' if both message and stack are empty or undefined.
    *   `detectEmptyRender(reports: readonly { type: string; result?: unknown }[] | undefined): boolean`
        *   Return true if any report has type 'render-analysis' and `result.emptyRender` is true.
    *   `toStoryTestResult(input: { storyId: string | undefined; statusRaw: string | undefined; errors?: readonly { message?: string; stack?: string }[]; reports?: readonly { type: string; result?: unknown }[] }): StoryTestResult | null`
        *   Return null if `storyId` is undefined.
        *   Normalize `statusRaw` to 'PASS', 'FAIL', or 'PENDING'.
        *   Set `emptyRender` to true only when status is PASS and `detectEmptyRender` is true.
        *   Extract error and stack from the first error object.

*   Implement utility functions in `code/core/src/shared/utils/analyze-test-results.ts`:
    *   `extractCategorizedErrors(testResults: StoryTestResult[]): { totalErrors: number; uniqueErrorCount: number; categorizedErrors: Record<string, { count: number; uniqueCount: number; matchedDependencies: string[] }> }`
        *   Process only FAIL results with non-empty error strings.
        *   Return totalErrors, uniqueErrorCount, and categorizedErrors.
    *   `analyzeTestResults(results: StoryTestResult[]): TestRunAnalysis`
        *   Return a summary object with total, passed, passedButEmptyRender, successRate, successRateWithoutEmptyRender, uniqueErrorCount, and categorizedErrors.

*   Define interfaces in `code/core/src/shared/utils/test-result-types.ts`:
    *   `StoryTestResult` with fields: storyId, status, error, stack, emptyRender.
    *   `TestRunAnalysis` with fields: total, passed, passedButEmptyRender, successRate, successRateWithoutEmptyRender, uniqueErrorCount, categorizedErrors.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.