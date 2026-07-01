## Description

The `vercel build` command produces build output in a `.vercel/output` directory. Currently, the output directory path is resolved as a relative path, which means the actual location on disk depends on the working directory at the time the command runs — not necessarily the project directory being built.

This causes bugs when the command is invoked with a different working directory than expected. For example, an API-only project ends up with an unexpected `static/` folder in its output (carrying over artifacts from a previous build of a different project), and the build manifest incorrectly lists extra builder entries. The build outputs are essentially being written to the wrong place.

## Expected Behavior

- Build outputs should always be written to `.vercel/output/` **relative to the project directory** (the working directory at the time `vercel build` is invoked), not relative to whatever directory the process happened to start in.
- An API-only project (with files only in `api/`) should produce function outputs in `.vercel/output/functions/api/` and should **not** produce a `.vercel/output/static/` directory.
- A static-only project should produce its files in `.vercel/output/static/` and only list the appropriate static builder in the build manifest.
- A project using a third-party builder should produce the correct function output directory structure, including a runtime configuration file inside each function directory, without creating a spurious static output directory.
- File paths used for builder detection should be normalized so builder matching works correctly on all platforms.

## Why This Matters

Developers using `vercel build` locally or in CI pipelines expect outputs to be isolated to each project's directory. When outputs bleed across projects or end up in the wrong location, deployments may silently include wrong files or miss expected function outputs, leading to hard-to-debug production issues.
