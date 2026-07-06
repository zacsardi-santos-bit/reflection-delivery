## Description

Conan currently supports generating toolchain configuration files for CMake and a few other build systems, but there is no equivalent support for the Meson build system. Developers who use Meson to build their projects and Conan to manage dependencies have no way to automatically bridge Conan settings (such as build type, compiler, and custom project options) into Meson's native machine file format.

## Expected Behavior

- A new toolchain class should be available from the top-level package so that a Conan recipe can instantiate it, assign typed project option values (strings, booleans, integers, and arrays), and write a Meson-compatible native configuration file during the install step.
- The generated native file must follow Meson's expected format: string values in single quotes, booleans as lowercase unquoted words, integers unquoted, and arrays as bracketed comma-separated single-quoted lists.
- Built-in Meson settings such as the build type must be translated correctly (e.g., Release maps to its lowercase equivalent).
- The generated file must be usable directly when invoking Meson setup, so that the project options and built-in settings flow into the actual build without manual intervention.

## Additional: Compiler detection in test source generator

The utility that generates test C++ source files should be extended to emit compiler identification information at runtime. Specifically, when GCC or Clang is used, the compiled binary should print the major and minor version numbers; on Apple platforms it should also print the Apple build version; and on x86 platforms it should indicate whether the 32-bit or 64-bit variant is active. This makes it possible to verify that the correct compiler and target architecture are actually in use during integration tests.

## Why This Matters

Without this support, Meson-based projects cannot take full advantage of Conan's build settings management. Developers are forced to manually synchronize build configurations between Conan and Meson, which is error-prone and prevents automated CI pipelines from working reliably with both tools together.
