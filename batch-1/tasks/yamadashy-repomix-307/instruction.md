Refactor the repository packaging tool to improve performance by parallelizing heavy processing steps using a worker thread pool. Each major operation should have its own dedicated worker module, and the orchestrating function should dispatch tasks to the pool. Implement a utility to determine the number of worker threads based on available CPU cores and task count.

*   Update `getProcessConcurrency` in `src/shared/processConcurrency.ts`:
    *   Return `os.availableParallelism()` directly, or `os.cpus().length` if unavailable.
*   Implement `getWorkerThreadCount` in `src/shared/processConcurrency.ts`:
    *   Accept `numOfTasks` and return `{ minThreads: number; maxThreads: number }`.
    *   Use formula: `Math.max(1, Math.min(getProcessConcurrency(), Math.ceil(numOfTasks / 100)))`.
*   Create `fileCollectWorker` module in `src/core/file/workers/fileCollectWorker.ts`:
    *   Export `MAX_FILE_SIZE` as `50 * 1024 * 1024`.
    *   Export `FileCollectTask` interface with `filePath` and `rootDir`.
    *   Default export: async function processing `FileCollectTask`.
*   Update `collectFiles` in `src/core/file/fileCollect.ts`:
    *   Add `progressCallback` as 3rd argument and `deps` object with `initTaskRunner` as 4th argument.
    *   Remove `MAX_FILE_SIZE` export.
*   Create `fileProcessWorker` module in `src/core/file/workers/fileProcessWorker.ts`:
    *   Export `FileProcessTask` interface with `rawFile` and `config`.
    *   Export `processContent` function.
    *   Default export: async function processing `FileProcessTask`.
*   Update `processFiles` in `src/core/file/fileProcess.ts`:
    *   Add `deps` object with `initTaskRunner` as 4th argument.
    *   Remove `processContent` export.
*   Create `fileMetricsWorker` module in `src/core/metrics/workers/fileMetricsWorker.ts`:
    *   Export `FileMetricsTask` interface with `file`, `index`, `totalFiles`, and `encoding`.
    *   Default export: async function returning `FileMetrics`.
*   Update `calculateAllFileMetrics` in `src/core/metrics/calculateAllFileMetrics.ts`:
    *   Change 2nd parameter to `TiktokenEncoding` string.
    *   Add `deps` object with `initTaskRunner` as 4th argument.
*   Create `outputMetricsWorker` module in `src/core/metrics/workers/outputMetricsWorker.ts`:
    *   Export `OutputMetricsTask` interface with `content`, `encoding`, and optional `path`.
    *   Default export: async function returning token count.
*   Implement `calculateOutputMetrics` in `src/core/metrics/calculateOutputMetrics.ts`:
    *   Accept `content`, `encoding`, optional `path`, and `deps` object.
    *   Return `Promise<number>` for token count.
    *   Log and re-throw errors.
*   Update `calculateMetrics` in `src/core/metrics/calculateMetrics.ts`:
    *   Add `deps` object with `calculateAllFileMetrics` and `calculateOutputMetrics`.
    *   Call `deps.calculateAllFileMetrics` and `deps.calculateOutputMetrics`.
*   Create `securityCheckWorker` module in `src/core/security/workers/securityCheckWorker.ts`:
    *   Export `SecurityCheckTask` interface with `filePath` and `content`.
    *   Export `createSecretLintConfig` and `runSecretLint`.
    *   Default export: async function returning `SuspiciousFileResult` or null.
*   Update `runSecurityCheck` in `src/core/security/securityCheck.ts`:
    *   Add `deps` object with `initTaskRunner`.
    *   Log progress and errors.
    *   Return empty array for empty input.
*   Update `validateFileSafety` in `src/core/security/validateFileSafety.ts`:
    *   Use `runSecurityCheck` from `securityCheck.ts` in `deps`.
*   Update `pack` in `src/core/packager.ts`:
    *   Call `collectFiles` with `progressCallback` as 3rd argument.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.