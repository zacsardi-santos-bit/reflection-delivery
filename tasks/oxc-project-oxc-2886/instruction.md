Implement a fix for the source map visualizer in the `oxc_sourcemap` crate to ensure that the last token mapping is included in the visualization output. Ensure that all token mappings, including the final one, are accurately represented in the generated text.

*   Update the `SourcemapVisualizer` struct in `crates/oxc_sourcemap/src/sourcemap_visualizer.rs` to include the last mapped token in its output.
    *   Use the `SourcemapVisualizer::new` method to initialize with the bundled output text and a reference to a `SourceMap`.
    *   Ensure `SourcemapVisualizer::into_visualizer_text` returns a `String` containing all token mappings, including the final token.
    *   After the main iteration loop, emit the final token separately to ensure it is included.

*   Ensure each token entry in the visualization:
    *   Shows a source range in the format `(src_line:src_col-end_line:end_col)`.
    *   Includes the quoted source text, followed by ' --> ', then the output range in the same format, and the quoted output text, terminated by a newline.
    *   Uses inclusive line range extraction for source text, including all characters up to the end column.
    *   Strips carriage return characters (`\r`) from extracted source text.

*   Handle multiple source files:
    *   Ensure the last token's range appears in the output, even if it spans multiple files.
    *   For the last token from a particular source file, extend the source range to the end of that file.

*   Access source map data using the public getter API:
    *   Use `SourceMap::get_tokens` to iterate over tokens.
    *   Use `SourceMap::get_sources` and `SourceMap::get_source` for source file names.
    *   Use `SourceMap::get_token` to retrieve the last token by index after the iteration loop.

*   Ensure the `SourceMap::from_json_string` method correctly parses a JSON v3 source map string and returns a `SourceMap`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.