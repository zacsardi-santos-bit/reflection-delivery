Implement a solution to normalize file paths in a VS Code extension for running tests, ensuring coverage overlays work correctly on Windows by converting drive letters to lowercase. Consolidate test result types and utilities into a unified module and enhance the TestResultProvider class with a public cache reset method.

*   Create a new directory `src/TestResults/` to unify test result types and utilities.
    *   Move `TestReconciliationState.ts` from its current location to `src/TestResults/`.
    *   Ensure `src/TestResults/` contains at least `index.ts`, `TestResult.ts`, `TestResultProvider.ts`, and the moved `TestReconciliationState.ts`.

*   Update `src/TestResults/index.ts` to export:
    *   `TestReconciliationState` as an object with string values `{KnownFail, KnownSkip, KnownSuccess, Unknown}`.
    *   `TestResult` as a type-only export (undefined at runtime).
    *   `TestResultProvider` as a function.
    *   `resultsWithLowerCaseWindowsDriveLetters` as a function.

*   Implement `resultsWithLowerCaseWindowsDriveLetters` in `src/TestResults/TestResult.ts`:
    *   Accept a `JestTotalResults` object.
    *   Return the same object unchanged on POSIX systems (`path.sep === '/'`).
    *   On Windows (`path.sep === '\\'`), return a new object spreading the input data, with `coverageMap` and `testResults` overridden by the results of `coverageMapWithLowerCaseWindowsDriveLetters` and `testResultsWithLowerCaseWindowsDriveLetters`, respectively.

*   Implement `testResultsWithLowerCaseWindowsDriveLetters`:
    *   Return `undefined` if input is `undefined`.
    *   Return the input unchanged if it is an empty array.
    *   For an array of test result objects, return a new array with each item's `name` property having its Windows drive letter lowercased if originally uppercase.

*   Implement `coverageMapWithLowerCaseWindowsDriveLetters`:
    *   Return `undefined` if `coverageMap` is absent.
    *   Return a new object mapping each coverage entry with both the object key and the `path` property having their Windows drive letter lowercased.

*   Implement `withLowerCaseWindowsDriveLetter`:
    *   Convert the drive letter to lowercase for paths with an uppercase Windows drive letter.
    *   Return `undefined` if no change is needed.

*   Enhance the `TestResultProvider` class:
    *   Expose a public `resetCache()` method to clear internal caches (`resultsByFilePath` and `sortedResultsByFilePath`).
    *   Modify `updateTestResults(data)` to call `this.resetCache()` and return the result of the underlying reconciler's status update.

*   Update existing consumers to import `TestResultProvider`, `TestResult`, and `TestReconciliationState` from `src/TestResults` instead of their previous locations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.