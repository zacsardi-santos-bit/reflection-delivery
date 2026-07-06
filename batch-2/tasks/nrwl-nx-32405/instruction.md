Implement improvements to the TypeScript plugin for a monorepo build system to handle projects without explicit output directories and to validate package build configurations. Ensure the plugin correctly infers build outputs and provides a utility function for configuration validation.

*   Implement the `isValidPackageJsonBuildConfig` function with the following signature:
    *   `isValidPackageJsonBuildConfig(tsConfig: ParsedTsconfigData, workspaceRoot: string, projectRoot: string): boolean`
    *   Located in `packages/js/src/plugins/typescript/util.ts`.
    *   Validate package build configurations by checking TypeScript compiler options against package.json entry points.
    *   Return `true` if the package is configured for building with compiled outputs, `false` otherwise.
    *   Return `true` unconditionally if no package.json exists at the project root.

*   Ensure `isValidPackageJsonBuildConfig` handles:
    *   Compiler options with `outFile` or `outDir` outside the project root, returning `true`.
    *   `outFile` within the project root, checking package.json exports or legacy fields for paths matching `outFile`.
    *   `outDir` within the project root, checking package.json exports or legacy fields for paths inside `outDir`.
    *   Neither `outFile` nor `outDir` set, checking exports or legacy fields for paths not matching tsconfig include patterns.
    *   Wildcard patterns in entry points that do not resolve to included source files.
    *   Undefined include patterns in tsconfig, allowing non-TypeScript file paths in package.json exports to return `true`.

*   Update the TypeScript plugin to infer build target outputs when no `outDir` is configured:
    *   Include glob patterns for compiled JavaScript files (`*.js`, `*.cjs`, `*.mjs`, `*.jsx`).
    *   Include glob patterns for source map files (`*.js.map`, `*.jsx.map`).
    *   Include glob patterns for TypeScript declaration files (`*.d.ts`, `*.d.cts`, `*.d.mts`).
    *   Include glob patterns for declaration map files (`*.d.ts.map`, `*.d.cts.map`, `*.d.mts.map`).
    *   Include the tsbuildinfo file.
    *   Scope all patterns to the project root.

*   Export the `ParsedTsconfigData` type from `packages/js/src/plugins/typescript/util.ts`:
    *   Signature: `{ options: Record<string, any>, projectReferences: any[], raw: Record<string, any>, extendedConfigFiles: any[] }`
    *   Represents parsed TypeScript configuration data, including resolved compiler options and raw tsconfig JSON.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.