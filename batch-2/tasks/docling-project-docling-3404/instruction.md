Implement parsers and utility functions to support new OCR model outputs in the document conversion pipeline. Create parsers for HTML and JSON formats, and develop utility functions for image dimension computation and stop-string removal.

*   Implement `parse_chandra_html` in `docling/utils/chandra_utils.py`:
    *   Accept `content` (str), `original_page_size` (Size), `page_no` (int), and `filename` (str).
    *   Parse top-level div elements with `data-bbox` and `data-label` attributes.
        *   Map labels to `DocItemLabel` values, defaulting unknown labels to `text`.
        *   Normalize bounding boxes from 0–1000 space to page coordinates.
    *   Handle 'Table' divs by parsing inner HTML tables.
    *   Expand 'List-Group' divs into individual list items.
    *   Skip divs with missing/invalid attributes or non-numeric bbox values.
    *   Return an empty `DoclingDocument` for empty or whitespace-only content.
    *   Ensure each text item has at least one provenance entry with non-negative bounding box coordinates.

*   Implement `parse_dots_json` in `docling/utils/dots_utils.py`:
    *   Accept `content` (str), `original_page_size` (Size), `page_no` (int), optional `model_image_size` (Size), and `filename` (str).
    *   Parse JSON arrays with 'bbox', 'category', and optional 'text'.
        *   Map categories to `DocItemLabel` values.
        *   Rescale bbox coordinates if `model_image_size` is provided.
    *   Use `_clean_json` to handle truncated or malformed JSON.
    *   Return an empty `DoclingDocument` for empty, whitespace-only, or invalid JSON.
    *   Skip non-dict elements and those with invalid bbox values.
    *   Parse HTML table content for 'Table' category items.

*   Implement `_clean_json` in `docling/utils/dots_utils.py`:
    *   Strip leading text before the first '['.
    *   Remove incomplete last array elements and close the array.
    *   Return '[]' if no '[' is found or input unchanged if valid JSON.

*   Implement `strip_stop_strings` in `docling/utils/vlm_utils.py`:
    *   Accept a list of `texts` and `stop_strings`.
    *   Truncate each text at the first occurrence of any stop string.
    *   Return unchanged text if no complete stop string is found.
    *   Return an empty list for empty input.

*   Implement `compute_qwen2vl_image_size` in `docling/utils/vlm_utils.py`:
    *   Accept `width` (int), `height` (int), and optional parameters `scale` (float), `max_size` (int), `max_pixels` (int), `min_pixels` (int), and `factor` (int).
    *   Ensure output dimensions are multiples of `factor`.
    *   Apply `scale` before rounding dimensions.
    *   Enforce `max_size` by clamping dimensions.
    *   Ensure total pixel area is within `max_pixels` and `min_pixels`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.