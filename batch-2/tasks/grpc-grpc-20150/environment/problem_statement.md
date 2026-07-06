## Description

The Bazel build rules for generating Python protobuf and gRPC bindings do not support proto library targets that remap their import paths using Bazel's prefix arguments. When a proto library places its output files in a virtual import directory (which is what Bazel does internally when import path remapping is requested), the downstream Python generation rules fail because they have no way to detect or handle that virtual directory structure.

## Expected Behavior

- The Python proto and gRPC library generation rules should be able to detect whether a given proto source file is located in a virtual imports directory.
- When such virtual import files are detected, code generation should correctly compute output paths and include directories relative to the virtual import root.
- Developers should be able to write proto libraries with remapped import prefixes and then use them as dependencies of Python proto and gRPC library targets without build failures.
- A utility function for detecting virtual import files should be added to the protobuf Bazel utility module so it is reusable across all code generation rules (Python, C++, Objective-C, etc.).

## Why This Matters

Many production proto APIs are organized under namespaced import paths. Without this support, teams cannot use Bazel's import path remapping for their proto definitions while also generating Python gRPC bindings. This is a significant gap for projects that organize their proto files under structured namespace paths.
