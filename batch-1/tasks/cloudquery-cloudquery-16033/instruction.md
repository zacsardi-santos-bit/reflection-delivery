Implement a utility to process markdown documents, identifying and replacing local image references with remote URLs after uploading. Ensure accurate detection of image references across various markdown syntax styles, handle file paths with special characters, and validate reference positions for consistency.

Requirements:

*   Implement `findMarkdownImages(contents string, dir string) (map[listKey][]reference, error)`:
    *   Return `nil` if no image references are found.
    *   Parse standard Markdown inline image syntax, returning a map entry keyed by `listKey` with `sum` as the SHA-1 hex digest and `name` as the image filename.
    *   Parse HTML `<img>` tags, handling single/double quotes and multiline tags. `startPos` and `endPos` should delimit only the `src` attribute value.
    *   Group multiple occurrences of the same image under the same `listKey`.
    *   Ignore image references inside code blocks or inline code spans.
    *   Ignore external image URLs (http/https).
    *   Return an error with the prefix 'error processing image' if a local image path cannot be found.
    *   Handle `file://` URLs with percent-decoded paths, supporting special characters like spaces and `@`.
    *   Handle reference-style Markdown images, with `startPos`/`endPos` pointing to the definition line. Deduplicate identical definitions.
    *   Handle linked images with `startPos`/`endPos` spanning the full outer-link tag.

*   Implement `replaceMarkdownImages(contents string, refs map[listKey][]reference) (string, error)`:
    *   Replace image references in the markdown string using `startPos`/`endPos` from `reference` structs, substituting with `url` values.

*   Implement `convertMarkdownReferences(refs map[listKey][]reference) ([]reference, error)`:
    *   Validate that reference ranges do not overlap and are valid.
    *   Return an error if any two references overlap, if `endPos` <= `startPos`, or if sorted positions overlap.

*   Define `reference` struct with fields:
    *   `ref` (string): image path as written in source.
    *   `absFile` (string): absolute file path.
    *   `url` (string): replacement URL.
    *   `startPos` (int): byte start of tag.
    *   `endPos` (int): exclusive byte end of tag.

*   Define `listKey` struct with fields:
    *   `name` (string): image filename.
    *   `sum` (string): SHA-1 hex digest of image contents.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.