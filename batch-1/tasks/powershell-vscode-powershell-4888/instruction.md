Refactor the PowerShell extension's debugger attach feature by consolidating the process-picker and runspace-picker logic directly into the main debug session feature class. Remove the two standalone picker classes and ensure the debug configuration resolver calls the new private methods on itself.

Requirements:

*   Update the `DebugSessionFeature` class in `src/features/DebugSession.ts`:
    *   Register VS Code commands directly in the constructor.
    *   Implement a private method `pickPSHostProcess()` that returns `Promise<number | undefined>`.
        *   Call `pickPSHostProcess()` within `resolveDebugConfigurationWithSubstitutedVariables` when no `processId` and no `customPipeName` are provided.
        *   If `pickPSHostProcess()` returns a number, assign it to `config.processId` and proceed.
        *   If `pickPSHostProcess()` returns `undefined`, return `undefined` to cancel the debug session.
    *   Implement a private method `pickRunspace(processId: number)` that returns `Promise<number | undefined>`.
        *   Call `pickRunspace(processId)` within `resolveDebugConfigurationWithSubstitutedVariables` when neither `runspaceId` nor `runspaceName` are provided.
        *   Pass the numeric `processId` as an argument to `pickRunspace`.
        *   If `pickRunspace()` returns a number, assign it to `config.runspaceId` and proceed.
        *   If `pickRunspace()` returns `undefined`, return `undefined` to cancel the debug session.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.