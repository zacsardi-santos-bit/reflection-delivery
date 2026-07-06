Implement functionality to ensure that footnote groups in JATS/NXML documents are correctly parsed and their content preserved in the conversion output. This includes handling footnote groups within document sections and ensuring their content appears in the Markdown export.

*   Extract and preserve text content from footnote-group elements (fn-group) containing footnote child elements (fn) in JATS document sections.
    *   Ensure footnote text is included in the converted document output.
    *   Display footnote text in the Markdown export, making it visible and readable.

*   Skip footnote groups that contain no footnote children without affecting the document conversion process.

*   Use the title element of a footnote-group as the section heading for the footnotes.
    *   Apply a default heading if no title element is present.

*   Process footnote-group elements in all document body sections, including back-matter sections with author notes.
    *   Ensure subsections like 'Competing interests', 'Author contributions', and 'Ethics' are included in the converted output with footnote text as list items.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.