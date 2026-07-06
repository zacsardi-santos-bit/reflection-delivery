I'm working on the experiment evaluation UI and noticed that when an evaluation run has no associated item, or when it has an item but no assertions are configured, the status field comes back empty rather than showing anything meaningful.

*   The getStatusFromExperimentItems function must return status ExperimentItemStatus.SKIPPED (not undefined) and skippedReason 'No experiment item defined' when the row has no experiment items (empty list), while still returning totalCount of 0.

*   The getStatusFromExperimentItems function must return status ExperimentItemStatus.SKIPPED and skippedReason 'No assertions defined' when experiment items exist but none of them have a status set (i.e., no assertions are defined on the items).

*   The getStatusInfoForExperiment function must return status ExperimentItemStatus.SKIPPED (not undefined) and skippedReason 'No experiment item defined' when the item argument is undefined, while still returning passedCount of 0 and totalCount of 0.

*   The getStatusInfoForExperiment function must return status ExperimentItemStatus.SKIPPED and skippedReason 'No assertions defined' when the item argument is provided but has no status set (i.e., no assertions are defined on the item).

*   The ExperimentItemStatus enum must include a SKIPPED variant.

*   The return type of both getStatusFromExperimentItems and getStatusInfoForExperiment must include a skippedReason field (string) that carries the human-readable reason why evaluation was skipped.


*   Interface details: Type: Function
Name: getStatusFromExperimentItems
Location: apps/opik-frontend/src/v2/pages-shared/experiments/EvaluationSuiteExperiment/PassedCell.tsx
Signature: getStatusFromExperimentItems(row: ExperimentsCompare) -> StatusInfo
Description: Computes the aggregate status across all experiment items in a row. When no items exist, returns status ExperimentItemStatus.SKIPPED with skippedReason "No experiment item defined". When items exist but none have a status, returns status ExperimentItemStatus.SKIPPED with skippedReason "No assertions defined".

Type: Function
Name: getStatusInfoForExperiment
Location: apps/opik-frontend/src/v2/pages-shared/experiments/EvaluationSuiteExperiment/PassedCell.tsx
Signature: getStatusInfoForExperiment(row: ExperimentsCompare, experimentId: string, item: ExperimentItem | undefined) -> StatusInfo
Description: Computes the status and counts for a specific experiment item within a row. When item is undefined, returns status ExperimentItemStatus.SKIPPED with skippedReason "No experiment item defined". When item has no status, returns status ExperimentItemStatus.SKIPPED with skippedReason "No assertions defined".

Type: Interface/Type
Name: StatusInfo
Location: apps/opik-frontend/src/v2/pages-shared/experiments/EvaluationSuiteExperiment/PassedCell.tsx
Description: The return type of both getStatusFromExperimentItems and getStatusInfoForExperiment. Must include: status (ExperimentItemStatus | undefined), assertionsByRun (AssertionResult[][]), passedCount (number), totalCount (number), and skippedReason (string, optional). The skippedReason field must be added to this type.

Type: Enum
Name: ExperimentItemStatus
Location: apps/opik-frontend/src/v2/pages-shared/experiments/EvaluationSuiteExperiment/PassedCell.tsx (imported and re-exported or defined in this module)
Description: Enum representing the result status of an experiment item evaluation. Must include at minimum SKIPPED, PASSED, and FAILED variants. SKIPPED must now be returned (instead of undefined) for the no-item and no-assertions cases.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.