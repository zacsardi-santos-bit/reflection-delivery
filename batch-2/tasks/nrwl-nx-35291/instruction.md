I'm running into an issue with how npm dependencies are resolved for projects that use a generic command runner to invoke the TypeScript compiler.

*   When a project's build target uses the generic command runner executor with a TypeScript compiler command (tsc or tsgo) that includes a --build argument pointing to a tsconfig file, the findNpmDependencies function must resolve the tsconfig path relative to the target's cwd option (or the project root if cwd is not specified) and read that project-level tsconfig to determine whether TypeScript helper imports are needed.

*   If the project-level tsconfig resolved from the build command has importHelpers set to true, findNpmDependencies must include tslib (with its version from the project graph's external nodes) in the returned dependencies object.

*   If the project-level tsconfig resolved from the build command has importHelpers set to false or not set, findNpmDependencies must NOT include tslib in the returned dependencies, even if the workspace root tsconfig has importHelpers set to true.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.