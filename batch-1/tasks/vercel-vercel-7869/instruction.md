Implement the `vercel build` command to ensure build outputs are correctly scoped to the project directory, avoiding cross-project contamination. Normalize file paths for builder detection to ensure compatibility across platforms.

*   Update the `build` function in `packages/cli/src/commands/build.ts`:
    *   Accept a `client` object and return a `Promise<number>`.
    *   Compute the output directory as an absolute path by joining the current working directory (`process.cwd()`) with the relative path `'.vercel/output'`.
    *   Ensure the build outputs are written to this computed directory.

*   Handle different project types:
    *   For static-only projects:
        *   Write a `builds.json` file to `<cwd>/.vercel/output/builds.json` with the content: `{ target: 'preview', builds: [{ require: '@vercel/static', apiVersion: 2, src: '**', use: '@vercel/static' }] }`.
        *   Copy static files to `<cwd>/.vercel/output/static/`.
    *   For Node.js API projects:
        *   Ensure `builds.json` contains only `@vercel/node` entries, one per API file, with the format: `{ require: '@vercel/node', apiVersion: 3, use: '@vercel/node', src: '<api file path>', config: { zeroConfig: true } }`.
        *   Do not create `<cwd>/.vercel/output/static/`.
        *   Create function output directories at `<cwd>/.vercel/output/functions/api/<name>.func` for each API endpoint file.
    *   For projects using third-party builders:
        *   Include an entry in `builds.json` for the third-party builder with the format: `{ require: '<builder name>', apiVersion: 3, use: '<builder@version>', src: '<matched file>', config: { zeroConfig: true, functions: { '<pattern>': { runtime: '<builder@version>' } } } }`.
        *   Do not create `<cwd>/.vercel/output/static/`.
        *   Ensure each built function produces a directory at `<cwd>/.vercel/output/functions/<path>.func/` containing a `.vc-config.json` file with `{ handler: '<entrypoint path>', runtime: '<runtime>', environment: {} }`.

*   Normalize file paths:
    *   Use forward slashes for source file paths to ensure builder detection works correctly on all operating systems.

*   Update `writeBuildResult` in `packages/cli/src/util/build/write-build-result.ts`:
    *   Ensure `outputDir` is used as an absolute path to write builder outputs, maintaining project scope.

*   Use the `OUTPUT_DIR` constant from `packages/cli/src/util/build/write-build-result.ts` to maintain consistency in output directory path resolution.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.