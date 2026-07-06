## Description

The Gaussian filter in MONAI currently only supports a single method for computing the discrete Gaussian kernel (via error-function interpolation). This is limiting for users who need other well-known Gaussian kernel approximations, such as a direct sampling approach or the scale-space discrete Gaussian based on modified Bessel functions. Additionally, there is no way to use the Gaussian filter as a trainable neural network layer, since the standard deviation is currently treated as a fixed constant rather than a learnable parameter.

## Expected Behavior

- The low-level Gaussian kernel function should accept an approximation-method parameter selecting among at least three discrete kernel types: the existing error-function approach, a sampled approach, and a scale-space approach (using modified Bessel functions). A normalization flag should also be supported to optionally normalize the kernel to sum to 1.
- Passing an unrecognized approximation method should raise a clear error.
- A polynomial evaluation utility function should be available from the layers module, supporting Python list, numeric array, and tensor inputs and differentiable with respect to both coefficients and inputs.
- The Gaussian filter module should support a trainability option so that sigma can be trained end-to-end via gradient descent. When enabled, sigma values should appear in the module's parameter list.
- All randomized Gaussian smoothing and sharpening transforms should expose the approximation method as a configurable parameter so users can select their preferred discrete kernel type.

## Why This Matters

Providing multiple discrete Gaussian kernel options allows researchers to match the mathematical properties of their chosen kernel to their application. Making sigma a trainable parameter enables data-driven optimization of filter bandwidth in learned models. These additions significantly improve the flexibility and composability of MONAI's Gaussian filter components.
