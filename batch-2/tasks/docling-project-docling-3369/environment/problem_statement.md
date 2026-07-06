## Description

The LaTeX document conversion backend currently has no way to render TikZ figures into actual images. When a LaTeX document contains TikZ diagrams, the converter either ignores them or handles them in a limited way. We should add support for using an external typesetting engine (Tectonic) to render TikZ code into images during conversion.

## Expected Behavior

- When the external typesetting engine is installed and available on the system path, the LaTeX backend should use it to render TikZ pictures into images.
- When the engine is unavailable, the backend should log a helpful installation hint.
- When rendering fails (timeout, runtime error, or the engine returns nothing), the backend should fall back gracefully by preserving the original TikZ markup as structured source code on the picture item, with the appropriate language tag.
- When the source document is file-backed (loaded from disk rather than a stream), the render engine should know the document's directory so it can locate locally referenced files.
- The render engine should handle incompatible PDF-specific directives in the document preamble by silently removing the problematic assignments and replacing them with explanatory comments, while leaving non-assignment uses of those directives intact.
- File dependencies referenced in the preamble or diagram code should be staged into the rendering sandbox, but only if they reside within the document's root directory — path traversal to files outside that boundary must be blocked.
- Shell escape behavior should be configurable; disabling it should prevent the shell-escape flag from being passed to the typesetting engine.

## Why This Matters

Without this feature, TikZ diagrams in LaTeX documents are lost during conversion. Supporting rendering (with a safe, graceful fallback) dramatically improves the fidelity of LaTeX document conversion, and the security constraints around file staging and path traversal ensure the rendering sandbox cannot be exploited to access files outside the document root.
