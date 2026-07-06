Implement a new React component to manage folder, file creation, and file upload actions in the storage browser. Update UI text labels for a more polished appearance and rename a shared data type to better reflect its purpose.

*   Create a new component named `CreateAndUploadAction` in `desktop/core/src/desktop/js/apps/storageBrowser/StorageDirectoryPage/CreateAndUploadAction/CreateAndUploadAction.tsx`.
    *   Accept props: `currentPath` (string), `onSuccessfulAction` (callback), and `setLoadingFiles` (callback with boolean).
    *   Render a button labeled 'New' that opens a dropdown with two groups: 'CREATE' and 'UPLOAD'.
    *   'CREATE' group includes 'New Folder' and 'New File'; 'UPLOAD' group includes 'New Upload'.
    *   Clicking 'New Folder' opens a modal titled 'Create New Folder'; 'New File' opens 'Create New File'; 'New Upload' opens 'Upload A File'.
    *   Folder and file creation modals must have a 'Create' button. On submission, call `save` with `{ path: currentPath, name: <entered name> }` and appropriate URL options.
*   Export constants `CREATE_DIRECTORY_API_URL` and `CREATE_FILE_API_URL` from `desktop/core/src/desktop/js/reactComponents/FileChooser/api.ts`.
*   Rename and export `StorageDirectoryTableData` from `desktop/core/src/desktop/js/reactComponents/FileChooser/types.ts`. Update all references from `StorageBrowserTableData`.
*   Modify `StorageBrowserActions` component in `desktop/core/src/desktop/js/apps/storageBrowser/StorageDirectoryPage/StorageBrowserActions/StorageBrowserActions.tsx` to accept `currentPath` prop.
    *   Update rename modal input label to 'Enter new name'.
*   Update `DragAndDrop` component in `desktop/core/src/desktop/js/reactComponents/DragAndDrop/DragAndDrop.tsx`:
    *   Default message: 'Drag and Drop files or browse'.
    *   Active drag message: 'Drop files here'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.