Implement a skill "inbox" feature for a CLI tool to manage auto-extracted skills. Create backend functions to list, move, and dismiss skills in the inbox. Develop an interactive UI dialog for users to review and act on these skills. Ensure proper error handling and notifications.

*   Implement the `listInboxSkills` function:
    *   Accepts a `Config` object and returns a `Promise` resolving to an array of `InboxSkill` objects.
    *   Each `InboxSkill` must include `dirName`, `name`, `description`, and `extractedAt`.
    *   Return an empty array if the inbox directory is missing or empty.

*   Implement the `moveInboxSkill` function:
    *   Accepts a `Config` object, a skill directory name, and a destination ('global' or 'project').
    *   Validates skill directory names to prevent path traversal attacks.
    *   Detects and handles name conflicts in the destination directory.
    *   Returns specific success or error messages based on the operation outcome.

*   Implement the `dismissInboxSkill` function:
    *   Accepts a `Config` object and a skill directory name.
    *   Validates the skill directory name.
    *   Returns specific success or error messages based on the operation outcome.

*   Export the `listInboxSkills`, `moveInboxSkill`, and `dismissInboxSkill` functions, as well as the `InboxSkill` type from the `@google/gemini-cli-core` package.

*   Modify the `startMemoryService` function:
    *   Emit an info-level feedback notification after extracting new skills using `coreEvents.emitFeedback`.

*   Update the memory slash command to include an 'inbox' subcommand:
    *   Return appropriate messages based on the experimental memory manager's status and configuration loading.

*   Develop the `SkillInboxDialog` component:
    *   Accepts `config`, `onClose`, and `onReloadSkills` as props.
    *   Displays inline error feedback for failed operations.
    *   Shows a disabled project destination option when the workspace is untrusted.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.