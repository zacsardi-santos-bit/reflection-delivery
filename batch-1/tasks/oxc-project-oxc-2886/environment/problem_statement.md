## Description

The source map visualizer, which generates human-readable text showing how tokens in bundled output map back to their original source files, is silently dropping the last entry in its output. No matter what source map is provided, the final token mapping is never included in the generated visualization text.

## Expected Behavior

- When visualizing a source map, every token mapping should appear in the output, including the very last one.
- The last token in a source file should show both its source range (e.g. the characters from some column to the end of the file) and its corresponding position in the bundled output.
- The visualization should be complete and not truncate or omit any trailing mappings.

## Current Behavior

The last token is always missing from the visualization output. For example, given a two-file bundle where the final source token spans from a mid-line position to the end of the file, that entry never appears in the visualization text.

## Why This Matters

Source map visualizations are used to verify and debug the correctness of bundler source maps. When the last token is silently dropped, developers cannot confirm that the end of each source file is correctly mapped in the output bundle. This makes it harder to diagnose source mapping issues that affect the end of files.
