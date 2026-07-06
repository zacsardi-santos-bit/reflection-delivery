I'm using the Python formatter on files that include special comments used by a document authoring tool.

*   When formatting Python code, comments whose text begins with the `|` character immediately after `#` (i.e., `#|...`) must be preserved verbatim — the formatter must not insert a space between `#` and `|`.

*   The verbatim preservation of `#|` comments must apply alongside the already-preserved special prefixes: `#!` (shebang-style), `#:` (Sphinx-style), `#'` (pweave-style), and `##` (double-hash). All of these must pass through unchanged.

*   When formatting a Quarto document file that is mapped to markdown formatting mode and contains a Python code block, any `#|` cell option comments within that block must remain unchanged while other Python code in the block is still reformatted according to normal formatting rules.

*   A fixture file at `crates/ruff_python_formatter/resources/test/fixtures/ruff/comment_prefixes.py` must exist containing examples of the four special comment prefix types (`#!`, `#:`, `#'`, `#|`), each on its own line.

*   A snapshot file at `crates/ruff_python_formatter/tests/snapshots/format@comment_prefixes.py.snap` must exist and must show that the formatted output of `comment_prefixes.py` is identical to its input (all special comment prefixes preserved verbatim).


*   Interface details: Type: Function
Name: normalize_comment
Location: crates/ruff_python_formatter/src/comments/format.rs
Signature: normalize_comment(comment: &SourceComment, source: &str) -> Result<Cow<str>, CommentError>
Description: Normalizes the content of a Python comment. Must preserve comments verbatim (without inserting a leading space) when the comment text starts with any of: ' ' (space), '!', ':', '#', '\'', or '|'. Comments starting with '|' (i.e., `#|` Quarto cell option comments) must be added to this preserved set.

Type: File
Name: comment_prefixes.py (fixture)
Location: crates/ruff_python_formatter/resources/test/fixtures/ruff/comment_prefixes.py
Description: Fixture input file for snapshot testing. Must contain lines demonstrating the four special comment prefix types that are preserved verbatim:
  - `#! shebang-style comments are left as-is`
  - `#: Sphinx-style comments are left as-is`
  - `#' pweave-style comments are left as-is`
  - `#| Quarto cell options are left as-is`

Type: File
Name: format@comment_prefixes.py.snap (snapshot)
Location: crates/ruff_python_formatter/tests/snapshots/format@comment_prefixes.py.snap
Description: Snapshot file for the comment_prefixes.py fixture test. The "Output" section must be identical to the "Input" section — all four special comment prefix lines must be preserved unchanged in the formatted output.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.