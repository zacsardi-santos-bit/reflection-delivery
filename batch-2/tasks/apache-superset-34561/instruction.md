Implement a feature to capture full-height screenshots of tall dashboards by scrolling through the content incrementally and stitching the resulting tiles into a single image. Develop two utility functions: one to combine image tiles vertically and another to manage the tiling process on a browser page.

*   Implement `combine_screenshot_tiles` function in `superset/utils/screenshot_utils.py`:
    *   Accept a list of PNG image byte strings.
    *   Return empty bytes if the list is empty.
    *   Return the single element unchanged if the list has one element.
    *   Combine multiple tiles vertically into a single PNG image:
        *   Width should be the maximum width of all tiles.
        *   Height should be the sum of all tile heights.
    *   Log any PIL error via `logger.exception` and return the first tile as a fallback.

*   Implement `take_tiled_screenshot` function in `superset/utils/screenshot_utils.py`:
    *   Accept a Playwright page object, a CSS selector string, and a `viewport_height` integer.
    *   Calculate the number of tiles as the ceiling of `element_height` divided by `viewport_height`.
    *   For each tile at index `i`:
        *   Scroll the page using `window.scrollTo(0, element_top + i * viewport_height)`.
        *   Call `page.wait_for_timeout(2000)` to wait 2000 milliseconds.
        *   Capture a screenshot with `type='png'` and a `clip` parameter derived from the element's bounding box.
    *   Reset the scroll position using `window.scrollTo(0, 0)` after capturing all tiles.
    *   Log the dashboard dimensions and position as 'Dashboard: {width}x{height}px at ({left}, {top})' and the tile count as 'Taking {n} screenshot tiles' via `logger.info`.
    *   Return the result of `combine_screenshot_tiles` when the tiling process completes successfully.
    *   Return `None` if the element cannot be found (exception during element wait).
    *   Log unexpected exceptions with 'Tiled screenshot failed: {error_message}' via `logger.exception` and return `None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.