## Description

Convolution operations do not validate that filter kernel dimensions must be strictly greater than zero. When a user accidentally constructs a filter with a zero-size kernel dimension and calls a convolution operation, the framework passes the invalid input directly to the hardware backend. On GPU, this results in an unhandled low-level crash rather than a clear, informative error message.

## Expected Behavior

- Both 1D and 2D convolution operations should detect a zero-size kernel dimension early in argument validation.
- When a zero-size kernel dimension is detected, a clear Python-level error should be raised immediately — before any hardware execution begins.
- This validation should apply to all supported configurations: standard convolutions, depthwise convolutions, all data formats, and all supported numeric precisions.

## Current Behavior

Passing a filter tensor with a zero-size spatial dimension (e.g., a kernel of height or width equal to zero) to a convolution operation causes a crash or opaque error at the hardware level, instead of producing a helpful validation message.

## Why This Matters

Without this guard, users who accidentally create a zero-size kernel get an unhelpful system-level error that is hard to diagnose. Adding early validation ensures users get a clear, actionable message identifying the invalid kernel dimension, regardless of which device or data type they are using.
