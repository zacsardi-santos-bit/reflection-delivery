## Description

When a Vite project is configured so that the application root is a subdirectory, developers sometimes need to glob-import files from directories that sit outside that root (e.g., a sibling or parent directory). Combining an absolute base path setting with a glob pattern that traverses outside the root currently doesn't work correctly — the imports either fail or produce an incorrect/empty module map.

## Expected Behavior

- Glob imports with a pattern that points to files outside the project root should work correctly when using an absolute base path option together with eager loading.
- The resulting module map should be keyed by the relative path from the importing file to each matched file (e.g., a path that traverses upward into the external directory).
- The exported values from those external files should be accessible via the module map.

## Steps to Reproduce

1. Set up a Vite project where the root is a subdirectory (e.g., a folder called "root").
2. Place some JavaScript files in a sibling directory outside the root (e.g., a folder called "external").
3. In the app, use a glob import with a pattern pointing outside the root directory (traversing up to the sibling directory to match all JS files) and pass an absolute base option.
4. Observe that the resulting module map is empty or the import fails entirely.

## Why This Matters

Projects that structure their source files across multiple directories — where the Vite root is not the top-level directory — need to be able to reference files outside the root via glob imports. This is a valid and useful pattern that should work reliably.
