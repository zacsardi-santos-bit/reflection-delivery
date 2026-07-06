Implement the necessary changes to enhance the bookmark management experience in the Android app by adding parent folder context to bookmarks, enabling inline folder creation, and providing folder lookup by ID.

*   Update the `BookmarkFoldersViewModel`:
    *   Implement the `newFolderAdded` method with the signature: `newFolderAdded(rootFolderName: String, selectedFolderId: Long, currentFolder: BookmarkFolder?)`.
    *   Ensure `newFolderAdded` calls `getFlatFolderStructure` on the bookmarks repository and updates the `folderStructure` field in the view state.
    *   Notify the view state observer at least twice when `newFolderAdded` is called:
        *   First with an empty `folderStructure` list.
        *   Then with the updated `folderStructure` list including the newly added folder.

*   Modify the `BookmarksRepository` interface and `BookmarksDataRepository` class:
    *   Implement the `getBookmarkFolderByParentId` suspend function with the signature: `suspend fun getBookmarkFolderByParentId(parentId: Long): BookmarkFolder?`.
    *   Ensure it returns the `BookmarkFolder` whose ID matches the given `parentId`, or `null` if not found.

*   Update the `Command` classes in `BrowserTabViewModel`:
    *   For `Command.ShowSavedSiteAddedConfirmation`, replace the direct `savedSite` property with a `savedSiteChangedViewState` property of type `SavedSiteChangedViewState`.
    *   For `Command.ShowEditSavedSiteDialog`, replace the direct `savedSite` property with a `savedSiteChangedViewState` property of type `SavedSiteChangedViewState`.

*   Define the `SavedSiteChangedViewState` data class in `BrowserTabViewModel`:
    *   Include at least a `savedSite` field of type `SavedSite` and a `bookmarkFolder` field of type `BookmarkFolder?` (nullable).

*   Ensure the `BookmarkFoldersViewModel.ViewState` data class includes a `folderStructure` field initialized as an empty list.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.