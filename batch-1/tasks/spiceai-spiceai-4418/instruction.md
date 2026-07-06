Update the `get_ai_accelerator` function to ensure that any detected GPU is eligible for hardware acceleration, regardless of its compute capability version. Remove the fixed allowlist of approved GPU versions and ensure that new GPU generations work automatically without requiring code updates.

*   Modify the `get_ai_accelerator` function in `bin/spice/pkg/github/runtime_release.go` to:
    *   Accept any detected CUDA compute capability version.
    *   Return a tuple with the accelerator identifier and a boolean indicating detection.
    *   Concatenate 'cuda_' with the raw version string from nvidia-smi for the accelerator identifier.
    *   Return `("cuda_91", true)` when a CUDA GPU is detected with a version string '91'.
    *   Return `("", false)` only when no GPU device is detected at all.

*   Ensure that:
    *   The function no longer relies on a fixed allowlist of approved GPU versions.
    *   Any previously excluded GPU version now returns true (found) if detected.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.