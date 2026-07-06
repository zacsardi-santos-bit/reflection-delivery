## Description

The project currently specifies a loose version range for the bundler as a development dependency. A newer patch release has been published and the project should be updated to pin to this specific newer version across all relevant package manifests.

## Expected Behavior

- The bundler dependency should be pinned to an exact version rather than a loose range in both the root package manifest and the test-tools package manifest.
- After running package installation with the updated dependency, the package manager's virtual store paths for all packages that list the bundler as a peer should reflect the new version.
- The CSS extraction plugin's pathinfo output — which embeds module source paths as comments inside compiled CSS files — must match the paths produced by the newly installed dependency tree.

## Why This Matters

When the bundler version changes, the package manager creates new content-addressable store paths for every package that lists the bundler as a peer dependency (such as the CSS loader). These paths are embedded verbatim into CSS output files when the pathinfo feature is active. If the package manifests are not updated to the new version, the installed dependency tree and the expected test output will diverge, causing the pathinfo snapshot tests to fail. Pinning to the correct newer version keeps the project current and the test expectations aligned with the actual installed packages.
