## Description

The uv project has introduced a dedicated, standalone build backend package that is separate from the main uv tool. However, when developers initialize a new Python application package and choose uv's built-in build backend, the generated project configuration file still references the old monolithic package as both the required build dependency and the build module name. This is incorrect: the new dedicated build backend package should be named instead.

## Expected Behavior

- When a new Python application package is initialized using uv's build backend, the generated build system section in the project configuration file should name the dedicated build backend package (not the main uv package) in the required build dependencies field.
- The build backend identifier in the build system section should also reflect the dedicated build backend package name.

## Why This Matters

Newly initialized projects will have build system configuration that points to the wrong dependency. Downstream tooling and users who install from source will attempt to fetch and use the wrong package as the build backend, which may not work correctly once the two packages diverge. The initialization command must be updated to emit the correct package name so that all newly created projects start with accurate build system metadata.
