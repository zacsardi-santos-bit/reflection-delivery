Restructure the glob-import playground in the Vite repository by moving all source files and configurations into a dedicated 'root/' subdirectory. Update the build tool and scripts to treat this subdirectory as the project root.

*   Move all source files and configurations into 'root/' within the playground/glob-import/ directory.
    *   Include index.html, vite.config, and directories like dir/, pkg-pages/, array-test-dir/, escape/, follow-symlinks/, side-effect/, imports-path/, subpath-imports-sub/, array-common-base/, and the local package dependency.
*   Update the Vite dev, build, and preview scripts in package.json to specify 'root' as the target directory.
*   Ensure Hot Module Replacement (HMR) functions correctly:
    *   Trigger HMR when files are added, edited, or removed in root/dir/, root/pkg-pages/, and root/array-test-dir/.
    *   Do not trigger HMR when files are added or removed in root/nohmr.js.
*   Direct build output to root/dist/ and update test helpers to use '../root/dist' as the directory argument.
*   Relocate escape test directories to root/escape/ and update directory listings to path.join(import.meta.dirname, '..', 'root', 'escape').
*   Update package.json:
    *   Modify 'imports' subpath field to './root/imports-path/*'.
    *   Change local workspace package dependency to 'file:./root/import-meta-glob-pkg'.
    *   Ensure the pnpm lockfile reflects the new location at playground/glob-import/root/import-meta-glob-pkg.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.