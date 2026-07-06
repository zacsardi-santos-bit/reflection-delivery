Implement a plain text output mode for a markdown query tool, allowing users to extract readable content without markdown formatting. Ensure that the tool can output plain text, markdown, or JSON formats as specified by the user.

*   Add a new output format option:
    *   Implement the `OutputFormat::Plain` variant in `src/cli.rs`, parsed from the string "plain".
    *   Allow selection via the `-o plain` or `--output plain` CLI flag.

*   Implement plain output mode:
    *   Create a public function `write_plain` in `src/fmt_plain.rs`.
        *   Signature: `write_plain<'md, I, W>(out: &mut W, nodes: I) where I: Iterator<Item = MdElemRef<'md>>, W: Write`.
        *   Write MdElemRef nodes as plain text, stripping all markdown formatting.
    *   Ensure inline formatting (emphasis, strong, strikethrough) is removed, leaving only raw text.
    *   Render links as their display text only, excluding URLs and brackets.
    *   Output code blocks as raw code content without fencing or language labels; empty blocks produce no output.
    *   Render table rows as space-separated cell values, with no formatting characters or header-separator rows.
    *   Collapse consecutive blank lines to a single blank line; ensure output ends with one trailing newline and no leading blank lines.

*   Enhance JSON output mode:
    *   Serialize code blocks as a JSON object with the key 'code_block', containing 'code', 'language', and 'type' fields.

*   Implement utility and helper structures:
    *   Define the `checked_elem_ref` macro in `src/utils_for_test.rs`.
        *   Signature: `checked_elem_ref!($input:expr => $variant:pat)`.
        *   Convert input to MdElemRef, panic if it doesn't match the variant pattern, and return the MdElemRef.
    *   Create the `NewlineCollapser` struct in `src/fmt_plain_writer.rs`.
        *   Implement `Write` for `NewlineCollapser<W>`.
        *   Methods:
            *   `new(underlying: W, max_newlines: usize) -> Self`
            *   `have_pending_newlines(&self) -> bool`
            *   `take_underlying(self) -> W`
        *   Collapse consecutive newlines to a configurable maximum, suppress leading newlines, and manage pending newlines.

*   Ensure compatibility with existing structures:
    *   Implement `From<&'md Inline> for MdElemRef<'md>` in `src/tree_ref.rs` to convert Inline elements for use with `checked_elem_ref!` and `write_plain`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.