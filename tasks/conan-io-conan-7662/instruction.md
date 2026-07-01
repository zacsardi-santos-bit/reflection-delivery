Implement a new toolchain class `MesonToolchain` in the Conan package to generate Meson-compatible native configuration files. Extend the utility for generating C++ source files to include runtime compiler identification.

Requirements:

*   Implement `MesonToolchain` class in `conans/client/toolchain/meson.py`.
    *   Ensure it is importable from the top-level package as `from conans import MesonToolchain`.
    *   Constructor signature: `__init__(self, conanfile, env=os.environ) -> None`.
    *   Expose a public attribute `definitions` as a dictionary for project option values of types str, bool, int, or list.
    *   Implement `write_toolchain_files(self) -> None` to write `conan_meson_native.ini` in the current working directory.
*   Ensure `conan_meson_native.ini` file format:
    *   Include `[project options]` and `[built-in options]` sections.
    *   Format string values with single quotes (e.g., `STRING_DEFINITION = 'Text'`).
    *   Format boolean values as lowercase words (e.g., `TRUE_DEFINITION = true`, `FALSE_DEFINITION = false`).
    *   Write integer values without quotes (e.g., `INT_DEFINITION = 42`).
    *   Format list values as bracketed, comma-separated lists of single-quoted strings (e.g., `ARRAY_DEFINITION = ['Text1', 'Text2']`).
    *   Map 'Release' build type to `buildtype = 'release'` in `[built-in options]`.
*   Ensure the compiled binary outputs `hello: Release!` and `STRING_DEFINITION: Text` after a successful Meson build using the generated file.
*   Update `gen_function_cpp` in `conans/test/assets/sources.py`:
    *   Add conditional preprocessor blocks to print compiler macros at runtime:
        *   `__GNUC__` → prints "{name} __GNUC__{value}"
        *   `__GNUC_MINOR__` → prints "{name} __GNUC_MINOR__{value}"
        *   `__clang_major__` → prints "{name} __clang_major__{value}"
        *   `__clang_minor__` → prints "{name} __clang_minor__{value}"
        *   `__apple_build_version__` → prints "{name} __apple_build_version__{value}"
        *   `__i386__` → prints "{name} __i386__ defined"
        *   `__x86_64__` → prints "{name} __x86_64__ defined"
*   On Linux with gcc 5 and x86_64 architecture, ensure the compiled demo outputs `main __x86_64__ defined` and `main __GNUC__5`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.