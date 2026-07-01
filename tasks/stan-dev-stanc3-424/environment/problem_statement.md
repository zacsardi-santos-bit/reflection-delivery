## Description

When a Stan model defines variables in the transformed data block and then passes those variables to generalized linear model (GLM) distribution functions in the model block, the OpenCL (GPU-accelerated) backend fails to generate the correct optimized code. Variables declared in the transformed data block are not recognized as candidates for GPU offloading, so GLM computations silently fall back to CPU execution even when the user intends them to run on the GPU.

This is inconsistent with how raw data block variables are handled — those are correctly promoted to their GPU-buffer equivalents for GLM calls. If a user pre-processes input data in a transformed data block (a very common pattern), they cannot benefit from GPU acceleration for the resulting GLM computations.

## Expected Behavior

- Variables declared in the transformed data block that are eligible for GPU acceleration (design matrices, response vectors, scalars) should have corresponding GPU buffer representations generated in the compiled C++ model class.
- These GPU buffers should be initialized in the model constructor.
- GLM distribution function calls that receive transformed data design matrices or response variables should use the GPU-promoted versions of those variables in the generated OpenCL code.
- The standard (non-GPU) C++ backend should continue to use transformed data variables directly in GLM calls.

## Why This Matters

Users frequently define derived data matrices and response vectors in the transformed data block to pre-compute features before running inference. If the compiler doesn't recognize these variables as GPU-eligible, models that rely on transformed data inputs to GLM functions will silently lose GPU acceleration, leading to unexpectedly slow inference and inconsistent behavior compared to models that avoid the transformed data block.
