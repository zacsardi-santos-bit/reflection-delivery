Update the monorepo build tool to support asynchronous operations and streaming of build results. Implement asynchronous versions of key functions to enable non-blocking behavior and continuous result emission in watch mode.

*   Implement `createProjectGraphAsync` in `packages/workspace/src/core/project-graph/project-graph.ts`.
    *   Export it as an asynchronous function returning a `Promise<ProjectGraph>`.
    *   Ensure it replaces synchronous calls in contexts requiring asynchronous project graph retrieval.

*   Update `ngPackagrLiteExecutor` in `packages/angular/src/executors/ng-packagr-lite/ng-packagr-lite.impl.ts`.
    *   Change the return type to `AsyncGenerator<{ success: boolean }>` or `AsyncIterableIterator`.
    *   In non-watch mode, ensure `.next()` resolves to `{ value: { success: false }, done: true }` if dependencies are unbuilt, or `{ value: { success: true }, done: true }` if built.
    *   In watch mode, yield successive build results without requiring type casting.

*   Modify `packageExecutor` in `packages/angular/src/executors/package/package.impl.ts`.
    *   Return an `AsyncGenerator<{ success: boolean }>` or `AsyncIterableIterator`.
    *   For non-watch mode, ensure `.next()` resolves to `{ value: { success: false }, done: true }` if dependencies are unbuilt, or `{ value: { success: true }, done: true }` if built.
    *   In watch mode, yield successive build results without requiring type casting.

*   Update `buildExecutor` in `packages/node/src/executors/build/build.impl.ts`.
    *   Return an `AsyncGenerator<{ success: boolean }>` or `AsyncIterableIterator`.
    *   Internally use `createProjectGraphAsync` for project graph retrieval.
    *   Handle webpack stats objects, ensuring builds are successful if `hasErrors()` returns `false`.

*   Refactor `checkDependencies` in `packages/workspace/src/generators/remove/lib/check-dependencies.ts`.
    *   Implement as an async function returning `Promise<void>`.
    *   Reject with an error message if dependents exist and `schema.forceRemove` is `false`, using the format: `${schema.projectName} is still depended on by the following projects:\n${dependentProjectNames}`.
    *   Resolve without error if `schema.forceRemove` is `true` or no dependents exist.
    *   Use `createProjectGraphAsync` to retrieve the project graph.

*   Ensure all synchronous calls to `createProjectGraph` in contexts needing async behavior are updated to use `createProjectGraphAsync`, including within `buildExecutor` and `checkDependencies`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.