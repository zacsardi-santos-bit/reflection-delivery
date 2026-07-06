Implement a source map generation feature in the test fixture system and create a utility to visualize source maps. Enable test fixtures to opt into source map output using a configuration flag, and produce a human-readable visualization of token-level mappings.

*   Create a `SourcemapVisualizer` struct in `crates/rolldown_sourcemap/src/sourcemap_visualizer.rs`.
    *   Export it as `rolldown_sourcemap::SourcemapVisualizer`.
    *   Implement the constructor `new(output: &str, sourcemap: &SourceMap) -> Self`.
    *   Implement the method `into_visualizer_text(self) -> String` to generate a human-readable visualization.
        *   Group entries by source file, with each file starting with a line '- <source_filename>'.
        *   Format each token mapping as '(<src_line>:<src_col>-<src_end_line>:<src_end_col>) "<src_text>" --> (<gen_line>:<gen_col>-<gen_end_line>:<gen_end_col>) "<gen_text>"'.
        *   Use 0-indexed line and column numbers and Rust debug-string formatting for text segments.

*   Update the `TestConfig` struct in `crates/rolldown_testing/src/test_config/mod.rs`.
    *   Add a new boolean field `sourcemap` with JSON key 'sourcemap', defaulting to false.
    *   When `sourcemap` is true, configure the bundler with `SourceMapType::File` for file-based source map output.

*   Modify the artifact snapshot behavior when the `sourcemap` flag is true.
    *   Append a section to the snapshot after the main code output, starting with '\n\n# Sourcemap Visualizer\n\n'.
    *   Include the `SourcemapVisualizer` text for all non-runtime output chunks with a source map.
    *   Join multiple chunk visualizations with '\n'.

*   Ensure the artifact snapshot code section only includes output chunks, not output assets, when rendering the main bundled code display.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.