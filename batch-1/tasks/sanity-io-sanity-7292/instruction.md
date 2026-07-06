Implement fixes and enhancements for the copy-paste feature in Sanity Studio to address existing gaps and bugs. Ensure proper validation, error handling, and modularization of recent searches storage.

*   Extract the `useStoredSearch` hook into a standalone module at `useStoredSearch.ts`.
    *   Export `useStoredSearch` as a named export with the signature: `useStoredSearch() -> [StoredSearch, (newValue: StoredSearch) => void]`.
    *   Export the `StoredSearch` type and `RECENT_SEARCH_VERSION` constant (value: 2).

*   Update recent searches functionality:
    *   Modify `addSearch` in `useRecentSearchesStore` to call `setStoredSearch` with objects shaped as `{ version: 2, recentSearches: [{ created: <ISO date string>, filters: [], terms: { query: string, typeNames: string[] } }] }`.
    *   Ensure `removeSearchAtIndex` does not modify storage when given an out-of-range positive index or a negative index.

*   Enhance the `useCopyPaste` hook:
    *   Ensure it exposes `setDocumentMeta`, `onPaste`, and `onCopy`.
    *   Make it accessible only within a `CopyPasteProvider` context.
    *   Export `CopyPasteProvider` from the main sanity package.

*   Implement paste operation validations and error handling:
    *   Validate referenced documents before pasting; show an error toast if a document does not exist, using the template 'The referenced document "{{ref}}" does not exist'.
    *   Adjust reference strength automatically when pasting between fields with different strength requirements.
    *   Show specific error messages for incompatible schema types or image types during paste operations.

*   Improve array field paste operations:
    *   Support both replacement and append modes for pasting into arrays.
    *   Generate appropriate patches (`setIfMissing`, `set`, `insert`) based on the paste type.
    *   Ensure new `_key` generation for pasted items in arrays.

*   Implement copy operation enhancements:
    *   Show a success toast with the title 'Field "<FieldTitle>" updated' upon successful paste.
    *   When copying an array item, show a toast with the title 'Item "<TypeTitle>" copied' and write a `SanityClipboardItem` to the clipboard with `patchType` set to 'append'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.