I'm working with Playwright's custom reporter API and I've noticed that when a worker-scoped fixture fails during teardown, the error callback on my reporter doesn't receive any worker context — the second parameter is undefined.

*   When a worker-scoped fixture teardown throws an error, the reporter's onError callback must be invoked with both the error object and a populated workerInfo object (not undefined).

*   The workerInfo object passed to onError during fixture teardown errors must have a workerIndex field of type number.

*   The workerInfo object passed to onError during fixture teardown errors must have a parallelIndex field of type number.

*   The workerInfo object passed to onError during fixture teardown errors must have a project field with a name property matching the configured project name.

*   The error object passed to onError during fixture teardown errors must have a message field containing the teardown error message text.

*   When a worker-scoped fixture teardown fails, the overall test run must exit with a non-zero exit code (exit code 1).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.