I'm working with marimo notebooks that have inline package dependency declarations embedded in the file header.

*   Must add a function `_header_for_script` in `marimo/_convert/script.py` that accepts a `NotebookSerialization` IR object and returns a string. When the IR originates from a Python notebook that contains a PEP 723 inline script metadata block (the `# /// script` comment block), the function must return that raw header text, preserving the `# /// script` marker and all listed dependencies.

*   When the IR originates from a Markdown-format notebook (i.e., the header field contains YAML frontmatter rather than raw comment text), `_header_for_script` must parse the YAML frontmatter and reconstruct the PEP 723 header by extracting the `pyproject` or `header` key from the parsed frontmatter using `get_headers_from_frontmatter` from `marimo._utils.inline_script_metadata`. The returned string must still contain the `# /// script` block and all listed dependencies.

*   If the IR has no header, `_header_for_script` must return an empty string.

*   The CLI `marimo export script` command run on a sandboxed marimo notebook must produce output (on stdout) that includes the full PEP 723 `# /// script` block, with all inline dependencies listed. The output must match the snapshot at `tests/_cli/snapshots/export/script/script_sandboxed.txt`.

*   The `convert_from_ir_to_script` function in `marimo/_convert/script.py` must use `_header_for_script` to obtain the header string, replacing the previous approach of reading header comments directly from a filename.


*   Interface details: Type: Function
Name: _header_for_script
Location: marimo/_convert/script.py
Signature: _header_for_script(ir: NotebookSerialization) -> str
Description: Extracts a Python script-appropriate header string from the notebook IR. For Python-origin notebooks, returns the raw header text stored in ir.header.value (which may contain a PEP 723 `# /// script` block and copyright comments). For Markdown-origin notebooks, parses the YAML frontmatter in ir.header.value and retrieves the PEP 723 header by looking for "pyproject" or "header" keys via get_headers_from_frontmatter. Returns an empty string if no header is present.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.