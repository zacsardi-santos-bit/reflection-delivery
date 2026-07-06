## Description

Two long-deprecated options in the Jest integration need to be formally removed and replaced with automated migration scripts so that existing workspaces can upgrade cleanly.

The first deprecated option is a generator flag that allowed users to opt out of generating a test setup file. This flag has been superseded by an equivalent value in the setup file selection option, so it is no longer needed in the generator schema. The second deprecated option is an executor-level configuration property that let users point to a test setup file by path. This behavior is now meant to live inside the Jest config file itself, under the standard Jest-native configuration, not in the project executor options.

## Expected Behavior

- A new migration script rewrites the old "skip setup file" generator default (when it was enabled) to its modern equivalent, and simply removes it when it was disabled. It must handle both the flat and nested forms of generator defaults in workspace configuration, handle project-level defaults, preserve unrelated generator entries, and clean up empty objects left behind after removal.

- A second migration script moves the deprecated executor setup file option into the Jest configuration file as a standard Jest-native entry. It must handle both CommonJS and ESM config formats, append to existing arrays without creating duplicates, handle spreads correctly, respect Jest's rootDir resolution rules, handle project configurations that override the config file, and gracefully bail (while still stripping the deprecated option) when the situation is too ambiguous to automate — returning a callback that can be used to surface a warning to the user.

## Why This Matters

Without these migrations, workspaces with either deprecated option set in their project or workspace configuration will break silently after upgrading, because the options are no longer read by the executor or generator. The migration scripts ensure a smooth, automated upgrade path for the vast majority of cases, with clear follow-up guidance for edge cases that require manual intervention.
