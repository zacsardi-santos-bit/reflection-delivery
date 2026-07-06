I'm working on a monorepo that uses a package manager configuration with a lenient failure policy — essentially saying "if the version doesn't match, just ignore it and continue.

*   The shouldPersistLockfile function must be exported from the @pnpm/config.reader package (i.e., from config/reader/src/index.ts), not from a standalone file in pnpm/src/.

*   The shouldPersistLockfile function must accept an optional onFail field in its parameter object. When onFail is 'ignore', the function must return false regardless of any other parameters (including version or fromDevEngines).

*   When fromDevEngines is true and onFail is not 'ignore', shouldPersistLockfile must return true regardless of the version string (including range formats like '>=9.0.0').

*   For the legacy packageManager field case (fromDevEngines not set or false): shouldPersistLockfile must return false for pnpm v11 or older, and true for pnpm v12 or newer. It must return false for a missing version (undefined), an invalid version string, or a range-format version string.

*   When onFail is 'ignore' and shouldPersistLockfile would return false, the self-update handler must not write packageManagerDependencies to the pnpm-lock.yaml file after resolving and installing the package manager.


*   Interface details: Type: Function
Name: shouldPersistLockfile
Location: config/reader/src/index.ts
Signature: shouldPersistLockfile(pm: Pick<WantedPackageManager, 'version' | 'fromDevEngines' | 'onFail'>): boolean
Description: Decides whether the resolved pnpm integrity info should be written to pnpm-lock.yaml under the project's packageManagerDependencies section. Returns false when pm.onFail === 'ignore'. Returns true when pm.fromDevEngines is true (and onFail is not 'ignore'). For the legacy packageManager field (fromDevEngines not set), returns false for versions older than v12 or for missing/invalid/range versions, and true for v12 or newer. Must be exported from the @pnpm/config.reader package.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.