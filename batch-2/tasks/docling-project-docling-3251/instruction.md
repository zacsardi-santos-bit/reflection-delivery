Implement support for custom block-level tags in the markdown conversion pipeline to handle 'signature' and 'stamp' tags. Ensure these tags are recognized and correctly processed during conversion and export.

*   Recognize and parse custom tags:
    *   Identify '<signature>TEXT</signature>' and '<stamp>TEXT</stamp>' as distinct elements in markdown input files.
    *   Do not treat these tags as plain text or ignore them.

*   Conversion and export requirements:
    *   For '<signature>TEXT</signature>':
        *   Convert and export to markdown as 'Signature' followed by a blank line and '<!-- image -->'.
    *   For '<stamp>TEXT</stamp>':
        *   Convert and export to markdown as 'Stamp' followed by a blank line and '<!-- image -->'.

*   Internal document representation:
    *   Preserve text content inside signature or stamp blocks as a text child element of the corresponding picture element.
        *   Store the text in both 'orig' and 'text' fields.
    *   For signature blocks:
        *   Create a picture element with 'meta.classification.predictions[0].class_name' set to 'signature'.
    *   For stamp blocks:
        *   Create a picture element with 'meta.classification.predictions[0].class_name' set to 'stamp'.

*   Maintain document structure:
    *   Ensure the overall document body ordering is preserved.
    *   Interleave text elements and signature/stamp picture elements in the same sequence as they appear in the input markdown file.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.