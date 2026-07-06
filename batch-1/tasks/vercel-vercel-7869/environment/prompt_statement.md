I'm running into a bug with the local build command in the Vercel CLI. When I run a build in one project directory and then run another build in a different project directory within the same process session, the outputs end up in the wrong places. An API-only project incorrectly ends up with a static output directory (it should not have one), and the build manifest lists builder entries that don't belong to that project.

The root cause seems to be that the output directory path is treated as relative rather than being anchored to an absolute path based on the current project's working directory. So when the working directory changes between builds, the output location drifts.

I'd like the build command to correctly resolve its output directory as an absolute path based on the project directory being built, so that each project's artifacts are always isolated to that project's own output directory. Additionally, file paths used for builder detection should be normalized to use forward slashes so matching works correctly across platforms.

Concretely, after a successful build:
- A static-only project should produce its static files in the correct output location and list only the appropriate static builder in the build manifest.
- An API-only project should produce function directories in the output, should NOT produce a static output directory, and should list only the API builder entries (not spurious static builder entries) in the build manifest.
- A project using a third-party builder should produce the expected function output directories with correct runtime configuration, and should not produce a spurious static output directory.
