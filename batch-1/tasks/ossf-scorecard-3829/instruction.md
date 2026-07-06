Refactor the dependency pinning check in the scorecard project to use a probe-based architecture. Implement a new probe to convert raw dependency data into structured findings, and update the evaluation function to accept these findings. Ensure the top-level check orchestrates the entire process using the new architecture.

*   Implement the `PinningDependencies` function in `checks/evaluation`:
    *   Accept parameters: `name` (string), `findings` ([]finding.Finding), and `dl` (checker.DetailLogger).
    *   Return a `checker.CheckResult`.
    *   Validate all findings have the probe name 'pinsDependencies' using `finding.UniqueProbesEqual`.
    *   Process findings:
        *   Positive outcome: produce an info log, score 10 for one pinned pip dependency.
        *   Negative outcome: produce a warn and an info log, score 0 for one unpinned pip dependency.
        *   Not-applicable outcome: produce a debug log, score -1 if it's the only finding.
        *   Error outcome: produce an info log.
        *   Two unpinned findings in the same ecosystem: two warn logs, one info log, score 0.
        *   Two unpinned findings in different ecosystems: two warn logs, two info logs, score 0.
        *   OutcomeNotAvailable: return an inconclusive result.
*   Implement the `PinningDependencies` function in `checks`:
    *   Accept a `*checker.CheckRequest` and return a `checker.CheckResult`.
    *   Orchestrate the full check using `zrunner.Run` with `probes.PinnedDependencies`.
    *   Ensure a Dockerfile with all pinned dependencies scores 10 with NumberOfInfo 1.
*   Create a new probe package `probes/pinsDependencies`:
    *   Implement `Run` function:
        *   Signature: `Run(raw *checker.RawResults) ([]finding.Finding, string, error)`.
        *   Convert raw dependency data into findings with appropriate outcomes.
        *   Return `sce.ErrScorecardInternal` for dependencies with neither location nor message.
        *   Include a `Values` map with `DepTypeKey` for each finding.
    *   Export constants: `Probe = 'pinsDependencies'` and `DepTypeKey = 'dependencyType'`.
    *   Implement `generateOwnerToDisplay` and `generateTextUnpinned` functions.
    *   Ensure a `def.yml` file exists in the directory for embedding.
*   Update `probes/entries.go`:
    *   Add `PinnedDependencies` as a slice of `ProbeImpl` containing `pinsDependencies.Run`.
*   Declare package-level variables in `checks/evaluation`:
    *   `var testLineStart = uint(0)`
    *   `var testSnippet = ""`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.