I'm working with an AsciiDoc document converter and I've found that tables with empty cells don't convert correctly to Markdown.

*   The AsciiDoc backend must correctly parse table rows that contain empty cells. Empty cells expressed as consecutive pipes ('||'), pipes with only whitespace ('| |'), or pipes with trailing spaces must be preserved as empty strings in the cell list, not filtered out.

*   When converting an AsciiDoc document with a table containing empty cells to Markdown using compact_tables=True, each empty cell must appear as an empty Markdown table cell (represented by whitespace between pipes) in the output.

*   The AsciiDoc test data files must be named using the 'asciidoc_NN.asciidoc' convention (e.g., asciidoc_01.asciidoc) stored in tests/data/asciidoc/. The corresponding ground truth Markdown files must use the naming pattern 'asciidoc_NN.asciidoc.md' stored in tests/data/groundtruth/docling_v2/.

*   The ground truth Markdown for asciidoc_01.asciidoc must use '#'-prefixed headings for titles and sections, list items formatted as '- item' without any leading asterisk, and compact table notation with '| - |' separator rows.

*   The ground truth Markdown for asciidoc_02.asciidoc must include properly indented nested lists, image blocks rendered as '<!-- image -->' comments, table captions as plain text paragraphs, and a table with multiple empty cells ('Table 5 with multiple empty cells') where all empty cells are preserved in the output.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.