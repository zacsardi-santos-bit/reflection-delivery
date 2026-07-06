Fix the correctness bugs in the Babel plugin for explicit resource management involving async disposal scenarios. Ensure that the compiled output behaves consistently with expected native implementations by addressing the following issues:

*   Implement error handling for invalid async disposal properties:
    *   Throw a TypeError with the message "Object is not disposable." when an async disposal property on a resource object is set to a non-function value, such as a number.
    *   Throw a TypeError with the message "Object is not disposable." when an async disposal property is explicitly set to null. Do not fall back to the synchronous disposal property in this case.
    *   Allow fallback to the synchronous disposal property only when the async disposal property is strictly undefined.

*   Optimize handling of multiple null async disposals:
    *   Ensure that when multiple null async resources are declared in 'await using' statements within a block, the compiled disposal logic handles them collectively without generating additional microtask roundtrips per null resource.
    *   Maintain a bounded total microtask overhead for these scenarios.

*   Correct behavior for sync fallback disposal in async contexts:
    *   Propagate errors from synchronous disposal methods as rejected Promises, not synchronously, to allow already-queued microtasks to execute before any surrounding catch block handles the error.
    *   Ignore any Promise returned by a synchronous disposal method used as a fallback in an 'await using' context. The disposal mechanism must not await this Promise, and execution should continue as if the method returned void.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.