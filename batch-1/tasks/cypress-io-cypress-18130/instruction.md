Implement a warning mechanism in the Cypress project initialization process to notify users running on a 32-bit Windows system about the upcoming deprecation of support for this platform. Ensure the warning is only triggered under specific conditions.

*   Update the `create` method in `packages/server/lib/open_project.ts` to detect a 32-bit Windows environment:
    *   Check if `os.platform()` returns 'win32'.
    *   Check if `os.arch()` returns 'ia32'.
*   If both conditions are met, invoke the `onWarning` callback provided in the `options` argument:
    *   Pass an object to the callback with a `message` property containing the text 'You are running a 32-bit build'.
*   Ensure the warning is not triggered for other platforms or for 64-bit Windows systems:
    *   Do not call the `onWarning` callback if the platform is not 'win32' or the architecture is not 'ia32'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.