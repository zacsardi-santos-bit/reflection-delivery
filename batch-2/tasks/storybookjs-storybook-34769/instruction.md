Implement utility functions to handle minimum-release-age restrictions for JavaScript package managers. These functions should parse configuration values, validate package release timestamps, compute release ages, find stable releases that meet age restrictions, check exclusion lists, generate user instructions, and extract error logs.

*   Implement `parsePositiveIntegerConfigValue` to:
    *   Accept a string, null, or undefined.
    *   Trim whitespace and return a positive integer if valid.
    *   Return null for '0', 'false', 'null', 'undefined', empty string, or non-positive values.
*   Implement `parseReleaseTime` to:
    *   Accept an unknown value.
    *   Return a Date object for valid ISO date strings.
    *   Return null for invalid date strings, non-string inputs, or empty strings.
*   Implement `parsePackageTimeMap` to:
    *   Accept an unknown value.
    *   Return a plain object mapping string keys to string values for valid objects.
    *   Return null for non-objects like strings, arrays, or null/undefined.
    *   Omit empty string values from the result.
*   Implement `getAgeInMinutes` to:
    *   Compute the floor of the difference in whole minutes between a reference Date and a published-at Date.
*   Implement `getLatestStableVersionAdheringToMinimumAgeGate` to:
    *   Accept a version-to-release-time map, a minimum age gate in minutes, and an optional reference time.
    *   Skip pre-release versions and return the latest stable version meeting the age restriction.
    *   Return null if no qualifying version is found.
*   Implement `hasStorybookMinimumAgeExclusions` to:
    *   Accept a list of configured package patterns.
    *   Return true if all core Storybook patterns are matched by at least one pattern in the list.
    *   Recognize glob-style wildcards as matching relevant package names.
    *   Return false if any core pattern is not matched.
*   Implement `getStorybookRerunInstruction` to:
    *   Return 'Please rerun Storybook creation with:' for 'create'.
    *   Return 'Please rerun the Storybook upgrade with:' for 'upgrade'.
*   Implement `getStorybookRerunCommand` to:
    *   Return 'npx create-storybook@{version}' for 'create'.
    *   Return 'npx storybook@{version} upgrade' for 'upgrade'.
*   Implement `getErrorLogs` to:
    *   Extract log text from an error object by joining non-empty `shortMessage`, `stderr`, and `message` fields with newline characters.
    *   Omit absent or empty fields from the result.
*   Export all new functions from `code/core/src/common/js-package-manager/util.ts`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.