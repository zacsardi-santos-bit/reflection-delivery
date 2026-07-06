Improve the Opentrons desktop app by replacing the current method for changing the Python interpreter path with a shell-level native directory picker. Move related actions to a dedicated protocol-analysis module to enhance code organization and maintainability.

*   Create a new protocol-analysis redux module:
    *   Location: `app/src/redux/protocol-analysis/`
    *   Include an index file that re-exports from an actions file.

*   Define constants in the protocol-analysis module:
    *   `OPEN_PYTHON_DIRECTORY`: 'protocol-analysis:OPEN_PYTHON_DIRECTORY'
    *   `CHANGE_PYTHON_PATH_OVERRIDE`: 'protocol-analysis:CHANGE_PYTHON_PATH_OVERRIDE'

*   Implement action creators in `app/src/redux/protocol-analysis/actions.ts`:
    *   `openPythonInterpreterDirectory()`: Returns an object with type `OPEN_PYTHON_DIRECTORY` and `meta: { shell: true }`.
    *   `changePythonPathOverrideConfig()`: Returns an object with type `CHANGE_PYTHON_PATH_OVERRIDE` and `meta: { shell: true }`.

*   Remove the `openPythonInterpreterDirectory` action creator from the config module:
    *   Ensure its test is removed from the config test suite.

*   Update the app-shell protocol-analysis module:
    *   Export `CONFIG_PYTHON_PATH_TO_PYTHON_OVERRIDE` with value 'python.pathToPythonOverride'.
    *   Implement `registerProtocolAnalysis(dispatch: Dispatch, mainWindow: BrowserWindow)`: 
        *   Read the current python override path from config.
        *   Register a config change watcher.
        *   Return an action handler function:
            *   On `CHANGE_PYTHON_PATH_OVERRIDE`: Call `showOpenDirectoryDialog(mainWindow)`. If non-empty, dispatch `updateConfigValue(CONFIG_PYTHON_PATH_TO_PYTHON_OVERRIDE, filePaths[0])`.
            *   On `OPEN_PYTHON_DIRECTORY`: Call `openDirectoryInFileExplorer` with the current path.

*   Implement `openDirectoryInFileExplorer` in `app-shell/src/dialogs/index.ts`:
    *   Accept a `string | null` directory argument.
    *   Return a `Promise<string | null>`. Resolve immediately with null if the argument is null.

*   Update the AdvancedSettings UI component:
    *   Dispatch `changePythonPathOverrideConfig` when 'Add override path' is clicked.
    *   Remove any hidden file input elements for directory selection.
    *   Dispatch `openPythonInterpreterDirectory` when the Python interpreter directory link is opened.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.