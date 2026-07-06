## Description

The bundler has existing support for generating source maps in its output, but there is no way to exercise or verify this functionality through the test fixture system. Test fixtures cannot currently opt into source map generation, and even when source maps are generated manually, there is no utility to inspect the resulting token-level mappings in a human-readable way.

Two capabilities are missing:

1. **Source map test configuration**: The test fixture system should support a configuration flag that enables source map generation for a given fixture. When enabled, the bundler should generate source map files alongside chunk output and include the standard mapping URL comment at the end of each chunk.

2. **Source map visualizer**: A utility is needed that can take generated code and its corresponding source map and produce a human-readable text representation. The visualization should group mappings by original source file and show, for each token, how a range of source text maps to a specific range of generated output text — including both position coordinates and the actual text content.

## Expected Behavior

- Test fixtures can opt into source map generation via a simple boolean flag in their configuration. When enabled, generated chunks will include a source mapping URL comment at the end of the code, referencing the corresponding map file by name.
- The sourcemap visualizer produces output grouped by source file, with each file introduced by a labeled header showing the source filename, followed by one line per token mapping showing both source and generated positions and text segments.
- When a fixture has source maps enabled, the test snapshot includes both the source map URL comment in the chunk code and a dedicated section showing the full token-level visualization.
- The artifact snapshot rendering correctly limits the code display to chunk outputs only (not raw asset outputs).

## Why This Matters

Without a way to enable and verify source maps in test fixtures, it's impossible to catch regressions in source map correctness. The visualizer also provides a clear, auditable record of how source code transforms map through to generated output, making it much easier to debug incorrect mappings.
