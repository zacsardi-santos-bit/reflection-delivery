Ensure that when using HTML files as webpack entry points with both HTML and CSS experimental features enabled, the generated HTML output includes the necessary stylesheet link tags for CSS imported through JavaScript. Implement the following requirements to address the issues with missing CSS links and attribute propagation:

*   Emit a stylesheet link tag in the output HTML for every CSS chunk associated with a JavaScript entry in that HTML file.
*   Copy security and fetch-related attributes (e.g., nonce, crossorigin, referrerpolicy) from the originating script tag to auto-generated stylesheet link tags.
    *   Do not copy script-specific attributes (e.g., defer, async, integrity) to stylesheet link tags.
*   Preserve all attributes of the original script tag in the output HTML, only modifying the src attribute to point to the emitted JS chunk.
*   Ensure that when CSS is split into multiple chunks, the HTML output contains one stylesheet link per CSS chunk, maintaining the original import order from the JavaScript source.
*   Position all stylesheet link tags before any script tags in the output HTML to ensure styles are loaded before scripts execute.
*   Maintain the order of explicitly authored stylesheet links and auto-generated links, ensuring authored links appear first, followed by JS-bundle CSS links, and then script tags.
*   Handle CSS @import statements inside JS-imported CSS files by bundling them into the same chunk as the importing CSS file without generating additional link tags.
*   When combining runtimeChunk and splitChunks (vendor) with JS-imported CSS, ensure script tags appear in the correct dependency order: runtime first, vendor second, entry last, with all stylesheet links preceding them.
*   Verify that all file references in the output HTML (stylesheet href and script src values) correspond to files emitted to the output directory.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.