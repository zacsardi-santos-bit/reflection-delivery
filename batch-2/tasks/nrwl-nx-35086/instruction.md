I'm running into a build caching issue in my Nx workspace where TypeScript's incremental compilation stops working after a cache hit.

*   When the TypeScript plugin infers task configurations (both typecheck and build targets), the outputs array for each task must include the path to the incremental compilation state file (.tsbuildinfo) for every tsconfig processed, regardless of whether an output directory is configured.

*   When no outDir is configured in the tsconfig, the tsbuildinfo file path must be '{projectRoot}/{configBaseName}.tsbuildinfo', where configBaseName is the tsconfig filename without the .json extension (e.g., tsconfig.json → '{projectRoot}/tsconfig.tsbuildinfo').

*   When outDir is configured in the tsconfig, the tsbuildinfo file path must be '{outDir}/{configBaseName}.tsbuildinfo' as a specific file path (not a glob or wildcard pattern). Previously, configurations with rootDir set used a glob pattern like '{outDir}/*.tsbuildinfo' which must now be replaced by the exact path.

*   When tsBuildInfoFile is explicitly set in the tsconfig options, that exact path must be used as the tsbuildinfo output path (translated to a {projectRoot} or {workspaceRoot} relative path as appropriate).

*   When outFile is configured in the tsconfig, the tsbuildinfo file path must be derived from the outFile location: the same directory as outFile, with the filename being the outFile basename (without .js) plus .tsbuildinfo.

*   When a project has multiple internal TypeScript project references (i.e., multiple tsconfigs within the same project), the outputs array must include the tsbuildinfo file path for each referenced tsconfig individually.

*   The tsbuildinfo output path must use the '{projectRoot}' token when the computed path falls within the project root directory, and the '{workspaceRoot}' token when the computed path is at the workspace level but outside the project root.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.