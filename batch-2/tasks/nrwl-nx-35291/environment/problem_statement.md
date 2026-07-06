## Description

When a library's build target uses a generic command runner executor to invoke the TypeScript compiler, the tool that determines which npm packages need to be bundled reads the wrong configuration file. It currently reads the workspace-level TypeScript configuration instead of the project-specific one to decide whether TypeScript helper imports are needed.

This means the dependency resolution is incorrect for projects that use the generic command runner with a project-level tsconfig:

- If the workspace root config does NOT require helper imports but the project's own config DOES, the TypeScript helper library is incorrectly omitted from the published package's dependencies.
- If the workspace root config DOES require helper imports but the project's own config does NOT, the TypeScript helper library is incorrectly included as a dependency.

## Expected Behavior

When a build command references a specific TypeScript configuration file via a build flag, the dependency resolver should:

- Resolve that config file relative to the working directory specified in the target options (or the project root if no working directory is set)
- Read that project-level config file to determine whether TypeScript helper imports are needed
- Include the helper library in the output dependencies if and only if the resolved project config enables helper imports

## Why This Matters

Projects that use the generic command runner to call the TypeScript compiler with a project-specific configuration need accurate dependency resolution. If the wrong tsconfig is read, libraries may ship with missing or extra dependencies, leading to runtime failures or unnecessary bloat in consuming applications.
