## Description

When exporting a marimo notebook to a standalone HTML file, audio and video elements that reference internal virtual files end up with broken, unresolvable URLs in the output. Only images are currently converted to embedded base64 data during export — audio and video are left as-is, which means media playback does not work in the exported file.

## Expected Behavior

- Audio and video files referenced through internal virtual file URLs should be embedded directly in the exported HTML as base64-encoded data, just like images already are.
- To prevent extremely large exported files, media files that exceed a reasonable size threshold (approximately 10 MB) should be replaced with a small informational placeholder rather than being fully embedded. The original internal URL should not appear in the final HTML in either case.
- The underlying helper that performs virtual-file-to-data-URI conversion should accept an optional file size limit parameter. When a file's size (encoded in its URL) exceeds the limit, a text/plain placeholder data URI should be substituted and the file should not be counted among the successfully inlined files.

## Why This Matters

Users who include audio clips or video in their notebooks currently get broken media when they export to HTML for sharing or offline use. Extending the inlining behavior to cover audio and video — and capping it at a sensible size — would make exported notebooks fully self-contained and playable without the notebook server running.
