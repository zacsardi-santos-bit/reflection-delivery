## Description

The glob-import playground needs to be restructured so that all of its source files live in a dedicated subdirectory, with the build tool explicitly pointed at that subdirectory as its project root. Currently, everything sits at the playground's top level, which makes it impossible to test scenarios where the project root differs from the directory that contains the workspace configuration.

## Expected Behavior

- All source files, configuration, and the local package dependency are relocated into a `root/` subdirectory within the playground.
- The dev, build, and preview scripts are updated to invoke the build tool with the `root` subdirectory as the explicit project root.
- Hot Module Replacement (HMR) correctly detects and responds to file additions, edits, and removals inside the new `root/` subdirectory.
- Build artifacts are emitted into a `dist/` folder inside `root/`, not at the old top-level location.
- The test that scans for special-character glob pattern directories looks inside `root/escape/` instead of at the top level.
- Package configuration paths (subpath imports and local dependency references) are updated to point into the new `root/` location.

## Why This Matters

This restructuring allows the playground to serve as a proper test for Vite's glob-import feature when the project root is a subdirectory — a common real-world pattern. Without it, the existing tests cannot cover this configuration, and HMR, build output, and glob resolution all assume a flat structure that does not match how many production projects are organized.
