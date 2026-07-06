Implement the specified changes to the release tooling for a JavaScript monorepo to support separate release pathways for main shell and auxiliary packages. Update functions to handle versioning and publishing tasks as described, ensuring error handling and git operations are correctly implemented.

Requirements:

* Implement `bumpMongoshReleasePackages` in `packages/build/src/npm-packages/bump.ts`:
    * Read the target version from `process.env.MONGOSH_RELEASE_VERSION`.
    * Throw an `Error` with the message 'MONGOSH_RELEASE_VERSION version not specified during mongosh bump' if the environment variable is empty or unset.

* Implement `updateShellApiMongoshVersion` in `packages/build/src/npm-packages/bump.ts`:
    * Accept a `version` string as a parameter.
    * Read the file at `path.join(__dirname, PROJECT_ROOT, 'packages', 'shell-api', 'src', 'mongosh-version.ts')`.
    * Replace the `MONGOSH_VERSION` constant value with the provided `version`.
    * Write the updated content back to the file using 'utf-8' encoding.

* Update `publishNpmPackages` in `packages/build/src/npm-packages/publish.ts`:
    * Change the first parameter to an options object with fields `isDryRun` and `useAuxiliaryPackagesOnly`.
    * If `useAuxiliaryPackagesOnly` is false and the package list does not include 'mongosh', throw an `Error` with the message 'mongosh package not found'.
    * When `useAuxiliaryPackagesOnly` is false, after calling lerna, create an annotated git tag for the mongosh version using `spawnSync('git', ['tag', '-a', <version>, '-m', <version>])` and push it with `spawnSync('git', ['push', '--follow-tags'])`.
    * When `useAuxiliaryPackagesOnly` is true, do not invoke git tag or git push commands.
    * Use the lerna command arguments: `['publish', 'from-package', '--no-private', '--no-changelog', '--exact', '--yes', '--no-verify-access']` and remove the flags `--no-push`, `--no-git-tag-version`, and `--force-publish`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.