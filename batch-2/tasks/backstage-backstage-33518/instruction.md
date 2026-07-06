I'm working on a CLI execute command for running scaffolder actions.

*   When executeCommand is called with no action ID argument and no help flag, it must throw an error with the message 'Action ID is required'.

*   When executeCommand is called with no action ID and no help flag, it must still invoke the CLI help display before throwing, but must not call auth resolution.

*   The CLI flags definition used by executeCommand must include a 'help' flag property so that the function can distinguish between help mode and execution mode (when flags.help is undefined, the function checks for a required action ID).


*   Interface details: Type: Function
Name: executeCommand
Location: packages/cli-module-actions/src/commands/execute.ts
Signature: executeCommand(context: { args: string[]; [key: string]: unknown }) -> Promise<void>
Description: Executes a scaffolder action via CLI. When called with no action ID in args and no help flag set, must show the CLI help display and then throw an error with the message 'Action ID is required'. When called with no action ID but help flag is set, shows generic help without throwing. Must not call auth resolution in either help scenario. This is the default export of the file.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.