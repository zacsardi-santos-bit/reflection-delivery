## Description

The naga shader compilation library currently has shader output backends (for Vulkan, Metal, DirectX, OpenGL/WebGL, and others) that are always compiled in based on explicit feature flags — regardless of whether the target platform even supports those backends. This means Metal shaders get compiled even on non-Apple platforms, and DirectX shaders get compiled even on non-Windows systems, which is unnecessary.

We should add support for platform-conditional backend compilation: Metal output should only be compiled when targeting Apple platforms, and DirectX output only when targeting Windows. At the same time, we should make it easier to express complex conditional compilation logic throughout the codebase by introducing short cfg aliases for each backend, so we don't have to repeat long feature flag expressions everywhere.

## Expected Behavior

- A build script should define short cfg aliases for each shader backend (for SPIR-V, MSL, HLSL, GLSL, WGSL, DOT output).
- Each alias resolves to the appropriate feature flag(s), plus optional platform conditions where relevant.
- New "target-platform conditional" features are available for Metal (Apple platforms only) and HLSL (Windows only), in addition to the existing unconditional features.
- All conditional compilation guards across the codebase use the new short aliases instead of verbose feature flag expressions.

## Why This Matters

This change reduces unnecessary compilation overhead and makes it possible to enable Metal/DirectX backends only when they are actually useful (i.e., when targeting the respective platform), while also cleaning up repeated boilerplate in conditional compilation attributes throughout the codebase.
