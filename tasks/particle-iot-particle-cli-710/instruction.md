Ensure consistent capitalization of "Logic Function" and "Logic Functions" across all user-facing text in the CLI tool. Implement an animated busy spinner for operations involving Logic Functions. Correctly parse the API response to capture the function's ID and version during deployment.

*   Update all user-facing text to use "Logic Function" or "Logic Functions" as a proper noun.
    *   Modify help descriptions, error messages, and interactive prompts accordingly.
    *   Ensure CLI command help descriptions use the capitalized form: 
        *   'Create, execute, and deploy Logic Functions'
        *   'Lists the deployed Logic Functions'
        *   'Downloads the Logic Function'
        *   'Creates a Logic Function'
        *   'Executes a Logic Function with user provided data'
        *   'Deploys a Logic Function to the cloud'
        *   'Disables a Logic Function in the cloud'
        *   'Enables a Logic Function in the cloud'
        *   'Deletes a Logic Function from the cloud'

*   Implement a busy spinner for Logic Function operations.
    *   In `src/cmd/logic-function.js`, modify the execute command to use `this.ui.showBusySpinnerUntilResolved` with the text 'Executing Logic Function <name> for <org>...' and the execute promise.

*   Correctly handle API responses for deploying Logic Functions.
    *   In `src/lib/logic-function.js`, update the `deploy()` method to extract `id` and `version` from `result.logic_function`.
    *   Ensure the response shape is `{ logic_function: { id, version } }`.

*   Update error handling for Logic Function operations.
    *   In `src/lib/logic-function.js`, modify the `listFromCloud()` method to throw 'Error listing Logic Functions: <original error message>' on failure.
    *   Modify the `getByIdOrName()` method to throw 'Logic Function not found' if no match is found.
    *   Update the `execute()` method to throw 'Error executing Logic Function: <original error message>' on failure.
    *   Update the `deploy()` method to throw 'Error deploying Logic Function: <original error message>' on failure.

*   Update prompts and error messages in `src/cmd/logic-function.js`.
    *   Ensure `_selectLogicFunctionName` uses 'Which Logic Function would you like to download?' and 'Provide name for the Logic Function'.
    *   Throw 'No Logic Functions found' when `_selectLogicFunctionName` is called with an empty list.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.