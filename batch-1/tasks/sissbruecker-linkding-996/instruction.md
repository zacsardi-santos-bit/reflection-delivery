Implement a new API endpoint to allow browser extensions to POST pre-captured HTML snapshots to the server, associating them with bookmarks. Ensure that file uploads are always available to bookmark owners, separate snapshot and upload logic into a dedicated service, and expose the application version in the user profile API response.

*   Create a new module `bookmarks/services/assets.py` with the following functions:
    *   `create_snapshot_asset(bookmark: Bookmark) -> BookmarkAsset`: Return an unsaved `BookmarkAsset` for an HTML snapshot with specific attributes.
    *   `create_snapshot(asset: BookmarkAsset) -> None`: Generate an HTML snapshot using `singlefile`, compress it, and save it in `LD_ASSET_FOLDER`. Handle exceptions by updating asset status.
    *   `upload_snapshot(bookmark: Bookmark, html: bytes) -> BookmarkAsset`: Compress HTML bytes, save them, and create a `BookmarkAsset`. Handle exceptions by not saving the asset.
    *   `upload_asset(bookmark: Bookmark, upload_file) -> BookmarkAsset`: Save the uploaded file, create a `BookmarkAsset`, and handle exceptions by not saving the asset.
*   Ensure all generated filenames are truncated to a maximum of 192 characters.
*   Modify `create_bookmark` in `bookmarks/services/bookmarks.py` to accept a `disable_html_snapshot` argument. Skip scheduling HTML snapshots when `disable_html_snapshot=True`.
*   Implement a new API endpoint at `bookmarks:bookmark-singlefile`:
    *   Accept POST requests with 'url' and 'file' parameters.
    *   Require authentication, returning HTTP 401 if unauthenticated.
    *   Return HTTP 400 with `{"error": "Both 'url' and 'file' parameters are required."}` if parameters are missing.
    *   Find or create a bookmark for the authenticated user with `disable_html_snapshot=True`.
    *   Call `assets.upload_snapshot(bookmark, file_bytes)` and return HTTP 201 with `{"message": "Snapshot uploaded successfully."}` on success.
*   Include a 'version' field in the user profile API response with the application version.
*   Update the bookmark details modal:
    *   Always show the asset list and individual asset information.
    *   Ensure the 'upload_asset' button is always present for owned bookmarks.
    *   Display the 'create_html_snapshot' button only when `LD_ENABLE_SNAPSHOTS=True`.
    *   Hide both buttons when viewing shared bookmarks owned by others.
*   Ensure the background task scheduler calls `assets.create_snapshot(asset)` for pending snapshot assets.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.