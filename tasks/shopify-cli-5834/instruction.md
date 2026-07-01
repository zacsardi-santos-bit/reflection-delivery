Implement a fault-tolerant parsing system for Shopify theme development server to handle Liquid syntax errors gracefully and enhance hot-reload change detection. Ensure the system can extract valid content from files with syntax errors and accurately track changes across all relevant parts of Liquid section files.

*   Export the `getUpdatedFileParts` function from `packages/theme/src/cli/utilities/theme-environment/hot-reload/server.ts`.
    *   Accept a `ThemeAsset` object with `key`, `checksum`, and `value` fields.
    *   Return an object with four boolean fields: `stylesheetTag`, `javascriptTag`, `schemaTag`, and `liquid` for Liquid files under `sections/`, `snippets/`, or `blocks/` prefixes.
    *   Return `undefined` for all other file types.
    *   For empty files, return all fields as `false`.
    *   On the first call for a file with content, set each field to `true` if that part's content is non-empty.
    *   On subsequent calls with unchanged content (same checksum), set all fields to `false`.
    *   Detect changes per-field independently; only set changed fields to `true`.

*   Implement fault-tolerant parsing for Liquid files.
    *   Log a debug message using `outputDebug` when parsing fails: 'Error parsing Liquid file "<key>" to detect updated file parts. LiquidHTMLParsingError:'.
    *   Use regex-based extraction for `stylesheet`, `javascript`, and `schema` tags if parsing fails.

*   Export the `fileDetailsCache` Map from `packages/theme/src/cli/utilities/theme-environment/hot-reload/server.ts`.
    *   Support clearing the cache with `.clear()`.
    *   Store entries per file key with: `checksum`, `stylesheetTag`, `javascriptTag`, `schemaTag`, and `liquid`.
    *   Normalize stored content: collapse multiple whitespace characters and trim the result.

*   Define the `liquid` field as all file content after removing specific blocks and comments, then normalize.
    *   Exclude: `stylesheet`, `javascript`, `schema` tag blocks, HTML comments, Liquid comment blocks, and Liquid doc blocks.

*   Ensure hot-reload event payloads for Liquid section files include an `updatedFileParts` field with four boolean fields.
*   Exclude `updatedFileParts` field from hot-reload event payloads for JSON files and pure asset files (CSS, JS).

*   Implement fault-tolerant aggregation for compiled asset endpoints.
    *   Log a debug message using `outputDebug` when parsing fails: 'Error parsing Liquid file "<key>" to extract <tag> tag. LiquidHTMLParsingError:'.
    *   Use regex-based extraction for valid tag content if parsing fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.