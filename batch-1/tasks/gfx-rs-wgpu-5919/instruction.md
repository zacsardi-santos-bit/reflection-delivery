Implement platform-conditional compilation for shader output backends in the naga shader compiler library. Introduce short cfg aliases for each backend to simplify conditional compilation logic. Update the build script and Cargo manifest accordingly.

*   Create a build script at `naga/build.rs` to define cfg aliases for shader output backends.
    *   Use the `cfg_aliases::cfg_aliases!` macro to declare the following aliases:
        *   `spv_out`: maps to `feature = "spv-out"`
        *   `hlsl_out`: maps to `any(feature = "hlsl-out", all(target_os = "windows", feature = "hlsl-out-if-target-windows"))`
        *   `msl_out`: maps to `any(feature = "msl-out", all(any(target_os = "ios", target_os = "macos"), feature = "msl-out-if-target-apple"))`
        *   `glsl_out`: maps to `feature = "glsl-out"`
        *   `wgsl_out`: maps to `feature = "wgsl-out"`
        *   `dot_out`: maps to `feature = "dot-out"`

*   Update `naga/Cargo.toml` with new conditional features and dependencies.
    *   Add the feature `msl-out-if-target-apple` under `[features]` to enable MSL output conditionally for Apple platforms.
    *   Add the feature `hlsl-out-if-target-windows` under `[features]` to enable HLSL output conditionally for Windows platforms.
    *   Include `cfg_aliases` as a build dependency under `[build-dependencies]`.

*   Refactor existing conditional compilation attributes in the source files.
    *   Replace all `#[cfg(feature = "spv-out")]` with `#[cfg(spv_out)]`.
    *   Replace all `#[cfg(feature = "hlsl-out")]` with `#[cfg(hlsl_out)]`.
    *   Replace all `#[cfg(feature = "msl-out")]` with `#[cfg(msl_out)]`.
    *   Replace all `#[cfg(feature = "glsl-out")]` with `#[cfg(glsl_out)]`.
    *   Replace all `#[cfg(feature = "wgsl-out")]` with `#[cfg(wgsl_out)]`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.