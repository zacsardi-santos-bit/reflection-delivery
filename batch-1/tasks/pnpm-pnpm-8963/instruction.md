Implement a new command in the pnpm project to display packages with ignored build scripts. This command should show both automatically suppressed builds during installation and explicitly configured skips in project settings.

*   Create a new package named `@pnpm/exec.build-commands` in the `exec/build-commands/` directory.
    *   Export an `ignoredBuilds` namespace from `exec/build-commands/src/index.ts`.
*   Implement the `ignoredBuilds` namespace with a `handler` function:
    *   Signature: `handler({ dir, modulesDir, rootProjectManifest }) => Promise<string>`.
    *   Parameters:
        *   `dir` and `modulesDir`: strings representing directory paths.
        *   `rootProjectManifest`: an object that may include a `pnpm.ignoredBuiltDependencies` string array.
*   Read the modules manifest from the `modulesDir` path using `@pnpm/modules-yaml`'s `readModulesManifest`.
    *   The manifest contains an `ignoredBuilds` string array for automatically suppressed packages.
*   Format the output string:
    *   Begin with 'Automatically ignored builds during installation:' followed by a newline.
    *   If `modulesDir` does not exist, display '  Cannot identify as no node_modules found' with a 2-space indent.
    *   If `ignoredBuilds` is empty, display '  None' with a 2-space indent.
    *   If `ignoredBuilds` is non-empty, list each package name with a 2-space indent, followed by:
        *   'hint: To allow the execution of build scripts for a package, add its name to "pnpm.onlyBuiltDependencies" in your "package.json", then run "pnpm rebuild".'
        *   'hint: If you don\'t want to build a package, add it to the "pnpm.ignoredBuiltDependencies" list.'
*   Handle `rootProjectManifest.pnpm.ignoredBuiltDependencies`:
    *   If non-empty, append a section 'Explicitly ignored package builds (via pnpm.ignoredBuiltDependencies):' followed by each package name with a 2-space indent.
    *   If absent, null, or empty, omit this section.
*   Ensure a blank line separates the automatically-ignored and explicitly-ignored sections when both are present.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.