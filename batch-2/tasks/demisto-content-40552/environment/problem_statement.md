## Description

The GreyNoise integration initializes the API client by passing a long list of individual connection settings as positional arguments. This makes the client hard to test, configure, and extend, and it tightly couples construction logic to callers. We should refactor to use a dedicated configuration object that bundles all settings together — the client then receives a single config object instead of a flat argument list.

Additionally, several command handlers need improvements:

- The business service intelligence lookup command nests its key result fields inside a sub-object, making them harder to consume. Key fields (IP address and the riot flag) should be promoted to the top level of the output.
- The query command uses an outdated output key format that is inconsistent with the rest of the integration.
- The IP quick-check, IP similarity, and IP timeline commands do not include the command name in their error messages for certain failure codes, making it hard to tell which command failed during incident response.
- The community integration variant needs a utility function to extract tag names from IP lookup responses.

## Expected Behavior

- A configuration object is created first with all connection settings, then handed to the client — the client no longer accepts a flat positional argument list.
- The integration entry point creates the configuration object and passes it to the client.
- The business service intelligence command returns output with the IP address and riot indicator at the top level.
- The query command uses the updated output key format.
- Error messages from the quick-check, similarity, and timeline commands include the name of the command that failed.
- The community integration exposes a function that extracts and returns tag names from IP data.

## Why This Matters

Clean initialization patterns and consistent error messages reduce confusion during debugging and make the codebase easier to maintain and test. Flattening the riot command output and fixing the query output key ensures consumers of the integration get data in the expected shape.
