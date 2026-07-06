Create a tool to automate the deployment of private packages in a monorepo after a release. Implement functions to read updated package data, identify deployable packages, execute deployments, and log the process. Ensure the tool integrates smoothly with the existing CI release process.

* Implement `getUpdatedPackages()` in `tools/deployments/deploy-non-npm-packages.ts`:
    * Return an empty array if the `PUBLISHED_PACKAGES` environment variable is not set.
    * Parse the JSON value from `PUBLISHED_PACKAGES` and return it as an array of `UpdatedPackage` objects.
    * Validate the parsed JSON:
        * Throw an `AssertionError` if the parsed value is not an array with the message: 'Expected PUBLISHED_PACKAGES to be an array but got {typeof value}.'
        * Throw an `AssertionError` if an array item is not an object with the message: 'Expected item {i} in array to be an array but got {typeof item}.'
        * Throw an `AssertionError` if an array item is missing a string `name` property with the message: 'Expected item {i} to have a "name" property of type string but got {item.name}.'
        * Throw an `AssertionError` if an array item is missing a string `version` property with the message: 'Expected item {i} to have a "version" property of type string but got {item.version}.'

* Implement `findDeployablePackageNames()` in `tools/deployments/deploy-non-npm-packages.ts`:
    * Scan the top-level `packages` directory of the monorepo.
    * Return a `Set<string>` of package names that have `private: true` and a `deploy` entry in their `scripts` field in `package.json`.

* Implement `deployPackage(pkgName: string)` in `tools/deployments/deploy-non-npm-packages.ts`:
    * Execute the shell command `pnpm -F {pkgName} deploy` using `execSync`.
    * Catch any error thrown by `execSync` and log it to `console.error` with the format: '::error::Failed to deploy "{pkgName}".' Do not rethrow the error.

* Implement `deployNonNpmPackages(updatedPackages: UpdatedPackage[], deployablePackageNames: Set<string>)` in `tools/deployments/deploy-non-npm-packages.ts`:
    * Log 'Checking for non-npm packages to deploy...' at the start.
    * For each updated package:
        * Log 'Package "{name}@{version}": deploying...' if the package name is in `deployablePackageNames` and call `deployPackage`.
        * Log 'Package "{name}@{version}": already deployed via npm.' if the package name is not in `deployablePackageNames`.
    * Log 'Deployed {count} non-npm packages.' if at least one package was deployed, or 'No non-npm packages to deploy.' if none were deployed.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.