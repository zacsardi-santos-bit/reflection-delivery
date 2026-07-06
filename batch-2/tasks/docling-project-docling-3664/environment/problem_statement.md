## Description

The AsciiDoc backend produces incorrect output when converting tables that contain empty cells. When a table row has consecutive empty cells or cells containing only whitespace, those cells are lost or mangled in the converted Markdown output. This means any AsciiDoc document with sparse tables (tables where not every cell has content) is not faithfully represented after conversion.

Additionally, the existing tests for the AsciiDoc backend do not actually verify the output — the assertions were commented out, so tests always passed regardless of what the converter produced. This means regressions in output quality go undetected.

## Expected Behavior

- Tables with empty cells should be preserved correctly in the Markdown output. A row where some cells are empty should produce a Markdown table row with those empty cells retained, not dropped or missing.
- The Markdown export should produce standard heading notation, clean list formatting (no spurious asterisk prefixes on list items), and compact table format.
- The test suite should compare the converted output against ground truth Markdown files, so any change in output quality is caught automatically.
- Test data files should follow a consistent naming convention.

## Why This Matters

Users converting AsciiDoc documents that include tables with optional or empty cells will get broken or incomplete Markdown output. Since tests were not enforcing correctness, these issues could go undetected for a long time and affect any downstream consumer of the converted documents.
