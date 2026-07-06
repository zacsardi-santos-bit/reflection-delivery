I'm working on refactoring the file attachment chip component in Streamlit's chat input so it becomes a shared, reusable component rather than something buried inside the chat input widget.

*   The file chip display component must be renamed from its chat-specific name to 'UploadedFileChip' and relocated to 'frontend/lib/src/components/shared/UploadedFile/UploadedFileChip.tsx'. It must be the default export of that module, with 'Props' as a named export.

*   The uploaded files container component must be renamed and relocated to 'frontend/lib/src/components/shared/UploadedFile/UploadedFileChips.tsx'. Its root element must use data-testid='stFileChips'.

*   The utility functions getFileTypeIcon, getFileExtension, isImageFile, truncateFilename, and useImagePreview must all be exported from a single module at 'frontend/lib/src/components/shared/UploadedFile/utils.ts'.

*   The UploadedFileChip component root element must use data-testid='stFileChip'. The filename display element must use data-testid='stFileChipName'. The delete button wrapper must use data-testid='stFileChipDeleteBtn'. The image preview element must use data-testid='stFileChipImagePreview'.

*   The UploadedFileChip component must set aria-label='{filename}, {sizeDisplay}' on the chip root element regardless of error state.

*   When a file has an error, the UploadedFileChip must set aria-invalid='true' and aria-describedby referencing an element that contains the text 'Error: {errorMessage}'. It must also render an element with role='alert'. When not in error, none of these attributes or elements should be present.

*   When the onRetry prop is provided and the file is in an error state, the chip root element must have role='button', tabindex='0', and title='Click to retry upload'. Pressing Enter on the focused chip must call onRetry exactly once.

*   The delete button must have aria-label='Remove {filename}' for uploaded files and include 'cancel upload' language for uploading files.

*   The getFileTypeIcon function must return the appropriate icon from @emotion-icons/material-outlined based on file extension (case-insensitive, using last segment for multi-dot filenames): Image for jpg/jpeg/png/gif/webp/svg/bmp, Article for pdf, TableChart for csv/tsv/xlsx/xls, Description for txt/md/json/xml/yaml/yml, Code for py/js/ts/jsx/tsx/css/html/java/cpp/c/go/rs/rb/php/swift/kt/scala/sh/bash/sql, MusicNote for mp3/wav/m4a/ogg/flac/aac, Videocam for mp4/webm/mov/avi/mkv/wmv, Folder for zip/tar/gz/rar/7z/bz2, and InsertDriveFile for unknown or missing extensions.

*   The getFileExtension function must return the lowercase extension without the dot. It must return an empty string for filenames with no extension, a trailing dot, or an empty string. For multi-dot filenames, it must return the last extension.

*   The isImageFile function must return true for extensions jpg, jpeg, png, gif, webp, svg, bmp (case-insensitive) and false for all others including files without an extension.

*   The truncateFilename function must accept an optional second parameter maxLength (default 20). It must return the filename unchanged if its length is at most maxLength. For longer filenames, it must use middle truncation with '...' as the ellipsis, preserve the file extension (last dot segment), and keep the result length at or below maxLength. For filenames without extension, it uses symmetric middle truncation. Edge cases: empty string returns '', single character returns unchanged, dot-only '.' returns '.'.

*   The useImagePreview hook must accept (file: File | undefined, filename: string) and return a string blob URL for image files (calling URL.createObjectURL), or null for non-image files or undefined file. It must revoke the blob URL via URL.revokeObjectURL on unmount and when the file prop changes. It must memoize the URL so the same file reference does not trigger additional createObjectURL calls.

*   The UploadFileInfo type/class must be importable from './UploadFileInfo' relative to the shared UploadedFile directory (i.e., 'frontend/lib/src/components/shared/UploadedFile/UploadFileInfo').


*   Interface details: Type: Module
Name: utils
Location: frontend/lib/src/components/shared/UploadedFile/utils.ts
Description: Shared utility module for uploaded file chip components, consolidating file type detection, filename truncation, and image preview functionality.

Functions exported from utils.ts:

Type: Function
Name: getFileTypeIcon
Location: frontend/lib/src/components/shared/UploadedFile/utils.ts
Signature: getFileTypeIcon(filename: string): EmotionIcon
Description: Returns an icon component from @emotion-icons/material-outlined based on the file extension (case-insensitive). Mapping: jpg/jpeg/png/gif/webp/svg/bmp → Image; pdf → Article; csv/tsv/xlsx/xls → TableChart; txt/md/json/xml/yaml/yml → Description; py/js/ts/jsx/tsx/css/html/java/cpp/c/go/rs/rb/php/swift/kt/scala/sh/bash/sql → Code; mp3/wav/m4a/ogg/flac/aac → MusicNote; mp4/webm/mov/avi/mkv/wmv → Videocam; zip/tar/gz/rar/7z/bz2 → Folder; unknown/missing extension → InsertDriveFile. Uses the last extension segment for files with multiple dots.

Type: Function
Name: getFileExtension
Location: frontend/lib/src/components/shared/UploadedFile/utils.ts
Signature: getFileExtension(filename: string): string
Description: Extracts and returns the lowercase file extension without the leading dot. Returns an empty string for filenames with no extension, a trailing dot, or an empty input. For files with multiple dots, returns the extension after the last dot.

Type: Function
Name: isImageFile
Location: frontend/lib/src/components/shared/UploadedFile/utils.ts
Signature: isImageFile(filename: string): boolean
Description: Returns true if the filename's extension (case-insensitive) is one of: jpg, jpeg, png, gif, webp, svg, bmp. Returns false for all other files including those without an extension.

Type: Function
Name: truncateFilename
Location: frontend/lib/src/components/shared/UploadedFile/utils.ts
Signature: truncateFilename(filename: string, maxLength?: number): string
Description: Truncates a filename using middle truncation, preserving the file extension. Default maxLength is 20. Returns the filename unchanged if its length is at most maxLength. For longer filenames, inserts "..." in the middle, keeping the start and end of the name, and always preserving the extension (last segment). For files without an extension, applies symmetric middle truncation. Result length must be <= maxLength.

Type: Function
Name: useImagePreview
Location: frontend/lib/src/components/shared/UploadedFile/utils.ts
Signature: useImagePreview(file: File | undefined, filename: string): string | null
Description: React hook that creates and manages a blob URL for image file previews. Returns a blob URL string (via URL.createObjectURL) for image files when a File object is provided. Returns null for non-image files or when file is undefined. Revokes the blob URL on unmount and when the file prop changes (preventing memory leaks). Memoizes the URL so the same file reference always yields the same URL without calling createObjectURL again.

---

Type: Component
Name: UploadedFileChip
Location: frontend/lib/src/components/shared/UploadedFile/UploadedFileChip.tsx
Description: A renamed and relocated version of the chat-specific file chip component. Renders a single uploaded file as a chip with accessibility attributes. Default export.
Signature (Props interface, named export):
  fileInfo: UploadFileInfo
  onDelete: (fileInfo: UploadFileInfo) => void
  onRetry?: (fileInfo: UploadFileInfo) => void

Rendered element test IDs and attributes:
- Root chip element: data-testid="stFileChip", aria-label="{filename}, {sizeDisplay}"
- When file has error: aria-invalid="true", aria-describedby pointing to element containing text "Error: {errorMessage}"
- When file has error: renders an element with role="alert"
- When file is not in error: no aria-invalid, no aria-describedby, no role="alert" element
- When onRetry is provided and file is in error state: chip has role="button", tabindex="0", title="Click to retry upload"; pressing Enter calls onRetry
- Filename element: data-testid="stFileChipName", title="{full filename}"
- Delete button wrapper: data-testid="stFileChipDeleteBtn"
- Delete button: aria-label="Remove {filename}" for uploaded files; "Cancel upload of {filename}" for uploading files
- Image preview (image files with File object present): data-testid="stFileChipImagePreview"

Type: Component
Name: UploadedFileChips
Location: frontend/lib/src/components/shared/UploadedFile/UploadedFileChips.tsx
Description: Container component rendering a list of UploadedFileChip items. Uses data-testid="stFileChips" on the root element. Renamed and relocated from the chat input widget.

Type: Module (re-exports)
Name: UploadFileInfo
Location: frontend/lib/src/components/shared/UploadedFile/UploadFileInfo.ts
Description: Already exists in the shared UploadedFile directory. The UploadedFileChip component imports UploadFileInfo from this same directory (./UploadFileInfo) rather than via an absolute alias.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.