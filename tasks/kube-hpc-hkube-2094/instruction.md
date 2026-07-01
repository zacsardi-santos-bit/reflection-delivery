Implement error handling and reporting for job creation failures in the task scheduling system. Ensure that invalid container configurations are detected, errors are captured, and user-friendly messages are propagated to pipeline nodes. Rename fields to accurately reflect their purpose and update related documentation.

*   Rename the field 'hasMaxCapacity' to 'surpassTimeout' in:
    *   All unscheduled algorithm warning objects produced by the task-executor reconciler.
    *   OpenAPI/Swagger specifications and resource response YAML definitions.

*   Update unscheduled algorithm warning objects to include:
    *   A 'code' field with appropriate warning categories: 
        *   `warningCodes.RESOURCES` for resource-based scheduling failures.
        *   `warningCodes.INVALID_VOLUME` for missing sidecar volumes.
        *   `warningCodes.JOB_CREATION_FAILED` for Kubernetes job creation errors.
    *   Source `warningCodes` constants from `@hkube/consts`.

*   Ensure the `@hkube/consts` package exports a `warningCodes` object with:
    *   `RESOURCES`
    *   `INVALID_VOLUME`
    *   `JOB_CREATION_FAILED`

*   Modify the Kubernetes job creation helper in `core/task-executor/lib/helpers/kubernetes.js` to:
    *   Return a structured result object: `{ statusCode: 200, job: jobDetails }` on success.
    *   Return `{ statusCode, message, jobDetails, spec }` on failure (e.g., statusCode 422).
    *   Avoid returning null on failure.

*   Update the reconciler to:
    *   Process results of job creation calls.
    *   Add algorithms to the skipped list with a warning for statusCode 422 failures containing:
        *   `reason: 'failedScheduling'`
        *   `surpassTimeout: true`
        *   `code: warningCodes.JOB_CREATION_FAILED`
    *   Count these as skipped (not created) in the reconcile result.

*   Reformat error messages for job creation failures by:
    *   Replacing 'Job.batch "<jobName>"' with 'Job'.
    *   Replacing 'spec.template.spec.containers[N]' with the actual container name at index N from the job spec.
    *   Removing quotes from numeric values.

*   Handle missing sidecar volumes by:
    *   Setting `reason: 'failedScheduling'` and `message: 'One or more sideCar volumes are missing or do not exist.\nMissing volumes: <comma-separated volume names>'`.
    *   Setting `surpassTimeout` to false or undefined.

*   Treat the sidecar container definition structure as:
    *   A single object with the shape `{ container: { name, image, resources? }, volumes?: [...] }`.

*   Ensure the pipeline driver:
    *   Sets the pipeline node's status to the algorithm's 'reason' field value.
    *   Sets the node's 'error' property to the algorithm's 'message'.
    *   Applies this error propagation to all batch items of the node.

*   Update the `_filterTasksByEvent` check in the pipeline driver to use 'surpassTimeout' instead of 'hasMaxCapacity' when deciding on immediate scheduling failure application.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.