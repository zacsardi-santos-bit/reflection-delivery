Implement support for rendering TikZ diagrams into images using the Tectonic typesetting engine in the LaTeX document conversion backend. Ensure that the system gracefully handles scenarios where the engine is unavailable or rendering fails, and maintain security by controlling file access.

*   Implement the `TectonicEngine` class in `docling/backend/latex/engines/tectonic.py`.
    *   Import `shutil` and `subprocess` at module scope for independent patching.
    *   Define `__init__(self, timeout: float = 60.0, allow_shell_escape: bool = True) -> None`.
        *   Use `shutil.which` to locate the Tectonic binary.
        *   Set `_is_available=True` and `binary_path=Path(<result>)` if found; otherwise, set `_is_available=False` and log a WARNING with 'Install Tectonic and make it available on PATH'.
    *   Define `is_available(self) -> bool` to return `self._is_available`.
    *   Define `render(self, tikz_code: str, preamble: str = "", source_root: Optional[Path] = None) -> Optional[Any]`.
        *   Invoke `subprocess.run` with `timeout=self.timeout`.
        *   Include '-Z' and 'shell-escape' in the command if `allow_shell_escape=True`; omit 'shell-escape' if `allow_shell_escape=False`.
        *   Exclude arguments starting with 'search-path='.
        *   Return `None` on `subprocess.TimeoutExpired` or `subprocess.CalledProcessError`.
        *   Apply `_sanitize_preamble_for_tectonic` to the preamble before writing the TeX file.
        *   When `source_root` is provided, stage local file dependencies referenced in the preamble and TikZ code into the render working directory, preserving relative paths; skip files outside `source_root`.
    *   Implement `_sanitize_preamble_for_tectonic(preamble: str) -> str` as a static or class method.
        *   Replace pdftex primitive assignment lines with comments in the format '% docling: removed for Tectonic compatibility: <original_line>'.
        *   Leave lines that reference or test a primitive unchanged.

*   Update `LatexBackendOptions` in `docling/backend/latex_backend.py` to accept `tikz_engine='tectonic'`.

*   Modify `LatexDocumentBackend` in `docling/backend/latex_backend.py`.
    *   Use `TectonicEngine` to render TikZ pictures when constructed with `LatexBackendOptions(tikz_engine='tectonic')`.
    *   Fall back to storing the original TikZ source as code on the picture item if `TectonicEngine.render()` returns `None` or raises an exception.
        *   Set `picture.image` to `None`.
        *   Set `picture.meta.code.text` with the TikZ markup, including '\begin{tikzpicture}'.
        *   Set `picture.meta.code.language.name` to 'TIKZ'.
    *   When backed by a file path, call `render()` with `source_root` set to the parent directory of that file.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.