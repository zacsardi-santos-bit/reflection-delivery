## Description

When using content-aware (smart) cropping to produce images at an exact target size, the output image can sometimes be 1–2 pixels smaller than requested. This happens because the smart crop analysis occasionally identifies a focus region that is slightly smaller than the target dimensions, and the system clips the result to that smaller region without expanding it back to the requested size.

For example, cropping a 900×562 image to exactly 900×561 using smart anchor may produce a 899×560 result instead. This is inconsistent with non-smart anchoring (e.g., TopLeft), which always returns the exact requested dimensions.

## Expected Behavior

- Smart crop and smart fill operations must return images with exactly the dimensions requested by the user, as long as the source image is large enough.
- If the smart crop analysis selects a region that is slightly smaller than the target, that region should be expanded to meet the target size while remaining within the source image bounds.
- Expansion should be distributed evenly between both sides of each axis, staying within the source image boundaries.
- If the source image is too small to accommodate the target size, the function should return the best available region without attempting to expand beyond the source.

## Why This Matters

Users who depend on predictable output dimensions (e.g., for layout grids or image galleries) get unexpectedly small images when using smart crop. This is a correctness bug — the smart crop mode should honor the requested dimensions just like any other anchor mode. Because this fix changes the output of the smart crop algorithm, previously cached smart-cropped images need to be regenerated, which requires bumping an internal version counter used as part of the image cache key.
