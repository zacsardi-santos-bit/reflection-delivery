## Description

The model compilation integration test is failing because hardware target specifications for GPU devices are being fully constructed as framework objects at the time the test module is imported, rather than at test execution time. This causes the entire test to error out on module load when the compilation backend isn't ready during import.

Additionally, the test only covers a subset of the supported deployment targets. Apple Metal, Android, and iOS are missing from both the target specification table and the output artifact suffix table, so compilation for those platforms cannot be tested at all.

## Expected Behavior

- Target specifications for complex hardware (such as CUDA, ROCm, and Vulkan devices) should be stored as plain data structures at module level, and only converted into framework-specific objects when the test actually runs.
- Simple targets (such as Metal, WebGPU, Android, and iOS) can be specified as plain string identifiers.
- The test function should handle both cases: if the target value is already a string, use it as-is; if it is a data structure, convert it to a framework target object and then to a string at test time.
- The test infrastructure should recognize the following new deployment targets, along with their expected compiled artifact file extensions:
  - Apple Metal → dylib
  - Android → tar
  - iOS → tar

## Why This Matters

Without this fix, the integration test fails to load at all, blocking compilation validation. Adding support for Metal, Android, and iOS ensures the test suite covers the full range of platforms that the project targets for deployment.
