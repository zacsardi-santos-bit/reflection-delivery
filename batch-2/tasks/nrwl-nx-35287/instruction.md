I'm working in a large Nx monorepo where our Playwright end-to-end test projects extend shared TypeScript configurations that live at the workspace root.

*   When building Playwright target inputs, the plugin must traverse the tsconfig 'extends' chain starting from the project's tsconfig.json and collect all tsconfig files that live outside the project root directory (i.e., files whose paths do not start with or equal the project root).

*   Each tsconfig file collected outside the project root must be added to the target's 'inputs' array as an object with 'json' set to '{workspaceRoot}/<workspace-relative-path>' and 'fields' set to ['compilerOptions', 'extends', 'files', 'include'].

*   The plugin must use getRootTsConfigFileName from '@nx/js' to determine which tsconfig file is handled by the native hasher; that file must be excluded from the collected inputs.

*   The workspace root tsconfig.json must be included in the collected inputs when it is NOT the native hasher file (i.e., when getRootTsConfigFileName returns 'tsconfig.base.json', not 'tsconfig.json').

*   Tsconfig files located inside the project root directory must not be added to the inputs array.

*   When a ciTargetName is configured alongside targetName, both the primary target and the CI target must receive the same tsconfig-related inputs.


*   Interface details: Type: Function
Name: getRootTsConfigFileName
Location: @nx/js (external package — must be imported from '@nx/js')
Signature: getRootTsConfigFileName() -> string
Description: Returns the filename of the workspace root tsconfig that is handled by the native Nx file hasher (e.g., 'tsconfig.base.json' or 'tsconfig.json'). The plugin must call this function from the '@nx/js' module. The test suite mocks this function via jest.spyOn on the '@nx/js' module namespace, so any implementation that does not call getRootTsConfigFileName from '@nx/js' will not be affected by the mock and the tests will fail.

Type: Object shape (inputs entry)
Name: tsconfigJsonInput
Location: packages/playwright/src/plugins/plugin.ts (added to target inputs)
Signature: { json: string, fields: string[] }
Description: Each external tsconfig file added to a target's inputs array must be an object with exactly two fields: 'json' set to the string '{workspaceRoot}/<workspace-relative-path>' and 'fields' set to the array ['compilerOptions', 'extends', 'files', 'include']. This exact shape is required by the test assertions.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.