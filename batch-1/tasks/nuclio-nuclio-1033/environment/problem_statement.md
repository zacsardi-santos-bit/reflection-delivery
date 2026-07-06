## Description

We need a way to redeploy an existing function without triggering a full image rebuild. Currently, every deployment always rebuilds the function image from source, even when nothing in the source code has changed and the user only wants to update configuration or re-run a previously deployed function. This is slow and unnecessary in cases where the image is already available.

Additionally, we need the platform to clearly reject attempts to use this "skip build" mode on functions that have never been built before — since there is no existing image to reuse, the deployment cannot proceed and must fail with an error.

## Expected Behavior

- When redeploying an existing function with the "never build" mode enabled, the platform should skip the build step and reuse the previously built image. The function's record of when it was last built should remain unchanged.
- After deployment completes (whether a build occurred or was skipped), the build mode setting should be cleared from the stored function configuration.
- When attempting to deploy a brand-new function (one that has never been built) with the "never build" mode, the platform should return an error and must not create or store any function record.

## Why This Matters

This feature allows operators to rapidly redeploy functions without the cost of rebuilding, while also tracking when the function image was last actually built. It also prevents silent misconfiguration where a user accidentally skips building a function that doesn't yet exist.
