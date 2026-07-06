## Description

Users with newer or less common GPU models are unable to benefit from hardware-accelerated AI inference, even though their GPU is perfectly capable. The system detects the GPU but then falls back to CPU processing because the GPU's compute capability version is not included in a hardcoded list of approved versions. This leads to unnecessary performance degradation.

## Expected Behavior

- Any detected GPU should be eligible for hardware acceleration, regardless of its compute capability version.
- The system should not maintain a fixed allowlist of approved GPU versions; new GPU generations should work automatically without requiring code updates.
- When a GPU is detected at runtime, the system should return a valid accelerator identifier and signal that an accelerator was found — regardless of whether the GPU version was previously known.

## Why This Matters

As new GPU hardware is released, the hardcoded allowlist becomes stale. Users with the latest GPU generations (which may have newer compute capability versions) are silently penalized — they see slower inference performance because the system defaults to CPU even when capable GPU hardware is present. Removing the version restriction ensures all users with a detectable GPU get hardware acceleration without requiring developers to manually update the approved list with each new GPU generation.
