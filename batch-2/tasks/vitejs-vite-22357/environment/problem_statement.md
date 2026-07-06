## Description

Vite's optimizer needs to run plugins that were originally written for a different bundler's API. These plugins commonly register end-of-build callbacks to inspect what was produced. When these plugins are converted to work inside Vite's pipeline, those end-of-build callbacks are not being invoked correctly — the result object passed to them is either missing or not shaped as the plugins expect.

## Expected Behavior

- When a plugin originally written for another bundler's API is converted to the format Vite uses internally, the conversion should preserve the end-of-build notification mechanism.
- After the build process completes, all end-of-build callbacks registered by the original plugin must be called.
- The callbacks must receive a result object that reflects the completed build state, including fields for output files, metadata, and mangle cache (all of which may be absent/undefined if not produced).

## Why This Matters

Plugins that depend on end-of-build hooks to perform post-processing, emit files, or read metadata will silently fail if the callbacks are never called or receive an incorrect result. Ensuring these callbacks are properly invoked with a well-formed result object makes converted plugins behave as their authors intended.
