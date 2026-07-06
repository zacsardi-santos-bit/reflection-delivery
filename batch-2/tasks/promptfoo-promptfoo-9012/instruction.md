I'm working on improving how email validation failures are handled in the CLI.

*   A new exported class EmailValidationError must exist in src/globalConfig/accounts.ts. Its constructor must accept two string arguments: a code (such as 'email_verification_required', 'exceeded_limit', or 'prompt_cancelled') and a human-readable message. It must behave as a standard Error subclass so it can be caught with normal try/catch.

*   EmailValidationError must be exported as a named export from the top-level src/index.ts module.

*   When the email prompt is cancelled by the user (i.e., an ExitPromptError is raised), promptForEmailUnverified must throw EmailValidationError with code 'prompt_cancelled' and message 'Email prompt cancelled.' instead of terminating the process. It must not set process.exitCode and must not produce any error or warning log output.

*   When checkEmailStatusAndMaybeExit determines that the user has exceeded the cloud inference limit, it must throw EmailValidationError with code 'exceeded_limit' and message 'You have exceeded the maximum cloud inference limit. Please contact inquiries@promptfoo.dev to upgrade your account.' It must still call logger.error with that message before throwing, and must not call process.exit or set process.exitCode.

*   When checkEmailStatusAndMaybeExit determines that email verification is required, it must throw EmailValidationError with code 'email_verification_required' and message 'Please verify your email address and try again.' It must still call logger.error with that message before throwing, and must not call process.exit or set process.exitCode.

*   In all other checkEmailStatusAndMaybeExit outcomes (show_usage_warning, bad_email, fetch errors), process.exitCode must remain unset (undefined).

*   A new exported function clearLogCallbackIfOwned must exist in src/logger.ts. It takes a single callback reference. If that reference is strictly equal to the currently active globalLogCallback, it clears the callback (sets globalLogCallback to null). If the reference differs from globalLogCallback, the active callback must be left unchanged.

*   globalLogCallback must be exported as a named variable from src/logger.ts so callers can inspect the currently active callback.

*   When doEval is running in watch mode and an EmailValidationError is thrown during email validation on a file-change event, the change handler must resolve to undefined (continue watching) rather than propagating the error. The configuration resolution must still be re-attempted on the next change.

*   When doRedteamRun's evaluation throws any error, it must call clearLogCallbackIfOwned with the logCallback option value (or null if no logCallback was provided) to clean up log state. It must also invoke the cleanup function returned by initVerboseToggle. The original error must be rethrown.

*   When the redteam run command receives an EmailValidationError from doRedteamRun, it must set process.exitCode to 1 and must not log any error message (since the error was already reported before being thrown).

*   When the redteam generate command receives an EmailValidationError from checkEmailStatusAndMaybeExit, it must set process.exitCode to 1 and must not log any additional error beyond what was already logged by checkEmailStatusAndMaybeExit.


*   Interface details: Type: Class
Name: EmailValidationError
Location: src/globalConfig/accounts.ts
Description: A recoverable error thrown when email validation fails. Extends the standard Error class. Must also be re-exported from src/index.ts as a named export.
Signature: constructor(code: string, message: string)

Type: Function
Name: clearLogCallbackIfOwned
Location: src/logger.ts
Description: Clears the active global log callback only if the provided reference is strictly equal to the currently active globalLogCallback. If the provided reference does not match globalLogCallback, the active callback is left unchanged.
Signature: clearLogCallbackIfOwned(callback: LogCallback | null): void

Type: Variable
Name: globalLogCallback
Location: src/logger.ts
Description: Exported named variable holding the currently active log callback, or null if none is set. Must be directly accessible as a named export so callers can inspect which callback is active.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.