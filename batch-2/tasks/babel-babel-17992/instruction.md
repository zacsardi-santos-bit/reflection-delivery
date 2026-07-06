I'm hitting a bug with Babel's source map output when transforming bundled JavaScript that already has an external source map.

*   When Babel transforms JavaScript code with 'inputSourceMap: true' and the external source map contains a 'names' array tracking variable identifiers, those original variable names must be preserved in the output source map even when a Babel plugin renames those variables.

*   When a variable tracked in the input source map's 'names' array is renamed by a transform to a conflict-avoiding name (e.g., an inner 'root' becomes 'root2$$$' to avoid collision with an outer 'root$$$'), the output source map must still map the renamed variable back to its original name from the input source map.

*   The output source map's 'names' array must include all original variable names from the source (e.g., 'marker', 'root', 'outer', 'test', 'inner') when those names are referenced in the output mappings.

*   The output source map 'mappings' field must use correct VLQ encoding that references the correct name indices, source positions, and lines — so that each renamed identifier in the output can be traced back to its original name and location in the pre-transformation source.

*   The output source map must preserve the 'ignoreList' field from the source (as an empty array when not set in the input source map).

*   The output source map must preserve 'sources', 'sourcesContent', and 'version' fields from the input source map, pointing back to the original pre-bundle source file.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.