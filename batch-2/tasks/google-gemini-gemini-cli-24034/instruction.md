Implement dynamic path retrieval for the task tracker feature in your project. Ensure the system prompt reflects the actual storage directory path by accessing the project configuration, and sanitize the path to prevent corruption in the system instructions.

*   Update the storage configuration object:
    *   Implement the `getProjectTempTrackerDir()` method in the storage object accessed via `config.storage`.
    *   Ensure this method returns the absolute filesystem path to the task tracker storage directory as a string.

*   Modify the `PromptProvider` class:
    *   In `PromptProvider.getCoreSystemPrompt(config)`, check if the task tracker feature is enabled using `config.isTrackerEnabled()`.
    *   If enabled, call `config.storage.getProjectTempTrackerDir()` to retrieve the tracker directory path.
    *   Sanitize the retrieved path:
        *   Replace newline characters (`\n`) with spaces.
        *   Remove closing bracket characters (`]`).
    *   Include the sanitized path in the system prompt within the '# TASK MANAGEMENT PROTOCOL' section using the phrase 'located at `{trackerDir}`'.
    *   If the task tracker feature is disabled, ensure the '# TASK MANAGEMENT PROTOCOL' section does not appear in the system prompt.

*   Ensure the `getCoreSystemPrompt(config: Config): string` method signature is maintained in `PromptProvider`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.