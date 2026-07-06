I'm working on improving the HTML export feature for marimo notebooks.

*   replace_virtual_files_with_data_uris must accept an optional max_inline_bytes parameter (integer or None). When None (the default), no size limit is enforced.

*   When max_inline_bytes is provided and the file size encoded in the virtual URL (the numeric prefix before the first hyphen, e.g. 9999999 in ./@file/9999999-large.wav) exceeds max_inline_bytes, the virtual URL must be replaced with a data:text/plain;base64, placeholder and the URL must NOT be added to the returned replaced set.

*   When the file size is within the max_inline_bytes limit, the virtual URL must be replaced with the appropriate data URI (e.g. data:audio/x-wav;base64, or data:audio/wav;base64, for .wav files, data:video/mp4;base64, for .mp4 files, data:image/png;base64, for .png files) and the URL must be added to the returned replaced set.

*   replace_virtual_files_with_data_uris must process audio and video tags (in addition to img) when they are included in the allowed_tags parameter. All three tag types must have their virtual file URLs replaced with data URIs.

*   The second element of the return value (the replaced set) must support both len() and the 'in' membership operator so callers can check which URLs were successfully inlined.

*   The HTML export functionality must inline audio virtual file URLs (e.g. ./@file/500-clip.wav) as base64 data URIs (data:audio/x-wav;base64, on Linux/macOS or data:audio/wav;base64, on Windows). The original virtual URL must not appear in the exported HTML. The base64-encoded audio content must be present in the exported HTML.

*   The HTML export functionality must apply a maximum inline size limit of 10 * 1024 * 1024 bytes (approximately 10 MB). Virtual files whose encoded size exceeds this limit must NOT be inlined as audio/video data URIs; instead they must be replaced with a data:text/plain;base64, placeholder and the original virtual URL must not appear in the exported HTML.


*   Interface details: Type: Function
Name: replace_virtual_files_with_data_uris
Location: marimo/_convert/common/dom_traversal.py
Signature: replace_virtual_files_with_data_uris(html: str, allowed_tags: set[str], allowed_attributes: Optional[set[str]] = None, max_inline_bytes: Optional[int] = None) -> tuple[str, set[str]]
Description: Replaces virtual file URLs (matching the `./@file/{size}-{filename}` pattern) found in the specified HTML tags with inline base64 data URIs. Returns a tuple of (processed_html, replaced_files) where replaced_files is the set of virtual URL paths that were successfully inlined. If max_inline_bytes is provided and a file's encoded size exceeds that limit, the URL is replaced with a data:text/plain;base64, placeholder instead and the URL is NOT added to the returned set.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.