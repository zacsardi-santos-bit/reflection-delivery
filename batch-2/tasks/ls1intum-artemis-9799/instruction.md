Implement a feature in the Artemis modeling exercise participation component that allows students to browse their submission history and view feedback for specific past submissions. Ensure the system correctly handles AI feedback integration and websocket result processing.

*   Update the `ModelingSubmissionService`:
    *   Implement the method `getSubmissionsWithResultsForParticipation(participationId: number): Observable<ModelingSubmission[]>`.
        *   Make a GET request to `api/participations/${participationId}/submissions-with-results`.
        *   Return an observable of `ModelingSubmission[]`, converting each submission from server format.

*   Modify the `ModelingFeedbackSuggestion` class:
    *   Change the last constructor parameter from `public elementIds: string[]` to `public reference: string | undefined`.
    *   Store a combined `type:elementId` string in `reference` or `undefined` for unreferenced feedback.

*   Process Athena feedback suggestions:
    *   Split the `reference` string on ':' to extract `referenceType` and `referenceId`.
    *   Set the feedback object's `reference` to the full string, `referenceId` to the portion after ':', and `referenceType` to the portion before ':'.
    *   For unreferenced suggestions, produce feedback with type `MANUAL_UNREFERENCED`.

*   Update `ModelingSubmissionComponent` properties:
    *   `isFeedbackView`: Boolean, true when `submissionId` is present in route parameters.
    *   `submissionId`: Number or undefined, set from route parameter `submissionId`.
    *   `sortedSubmissionHistory`: Array of submissions sorted by latest result's `completionDate` descending.
    *   `sortedResultHistory`: Array of results, one per submission, sorted by `completionDate` descending.

*   Handle component initialization:
    *   Without `submissionId` in route params (standard mode):
        *   Set `isFeedbackView` to false.
        *   Call `getLatestSubmissionForModelingEditor`.
    *   With `submissionId` in route params (feedback view mode):
        *   Set `isFeedbackView` to true.
        *   Set `submissionId` to the numeric value from params.
        *   Call `getSubmissionsWithResultsForParticipation(participationId)` and `getLatestSubmissionForModelingEditor(participationId)`.

*   Implement websocket result handling:
    *   For manual results with `completionDate`:
        *   Update `assessmentResult`.
        *   Call `alertService.info('artemisApp.modelingEditor.newAssessment')`.
    *   For successful Athena results:
        *   Update `assessmentResult`.
        *   Call `alertService.success('artemisApp.exercise.athenaFeedbackSuccessful')`.
    *   For failed Athena results:
        *   Call `alertService.error('artemisApp.exercise.athenaFeedbackFailed')`.
        *   Set `isGeneratingFeedback` to false.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.