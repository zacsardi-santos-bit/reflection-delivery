I'm running into an issue where importing a local directory path in an ES module gives a confusing low-level OS error instead of a proper, meaningful message.

*   When fetch_no_follow is called with a file:// URL that points to a directory, it must return an Err containing a FetchNoFollowError rather than returning Ok.

*   The FetchNoFollowError returned for a directory URL must, when converted to its kind via into_kind(), yield the FetchNoFollowErrorKind::UnsupportedDirImport variant containing an UnsupportedDirImportError whose url field equals the directory URL that was requested.

*   The UnsupportedDirImportError Display implementation must produce exactly the string: "[ERR_UNSUPPORTED_DIR_IMPORT] Directory import '{url}' is not supported resolving ES modules", where {url} is the actual directory URL.

*   UnsupportedDirImportError must be classified as a JavaScript TypeError (not a generic error), so that when the error propagates to JavaScript it is instanceof TypeError and does not appear as a raw OS error.

*   When a JavaScript ESM module attempts to import a local directory path, the resulting JavaScript error must include the text "ERR_UNSUPPORTED_DIR_IMPORT" in its string representation and must NOT include the text "os error".

*   FetchLocalErrorKind must also include an UnsupportedDirImport variant wrapping UnsupportedDirImportError, and this must propagate correctly through the FetchNoFollowErrorKind conversion so the directory-import error is surfaced at both the local and no-follow fetch levels.


*   Interface details: Type: Struct
Name: UnsupportedDirImportError
Location: libs/cache_dir/file_fetcher/mod.rs
Description: Error returned when a file URL pointing to a directory is fetched. Must implement Display with the exact format: "[ERR_UNSUPPORTED_DIR_IMPORT] Directory import '{url}' is not supported resolving ES modules". Must be annotated as a JavaScript TypeError (class "type").
Fields:
  pub url: Url  — the directory URL that triggered the error

Type: Enum Variant
Name: FetchNoFollowErrorKind::UnsupportedDirImport
Location: libs/cache_dir/file_fetcher/mod.rs
Description: A new variant of the existing FetchNoFollowErrorKind enum. Wraps an UnsupportedDirImportError. Must be returned by FetchNoFollowError::into_kind() when a directory URL is fetched.
Signature: UnsupportedDirImport(UnsupportedDirImportError)

Type: Enum Variant
Name: FetchLocalErrorKind::UnsupportedDirImport
Location: libs/cache_dir/file_fetcher/mod.rs
Description: A new variant of the existing FetchLocalErrorKind enum. Wraps an UnsupportedDirImportError. Must propagate into FetchNoFollowErrorKind::UnsupportedDirImport via the existing From<FetchLocalError> conversion.
Signature: UnsupportedDirImport(UnsupportedDirImportError)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.