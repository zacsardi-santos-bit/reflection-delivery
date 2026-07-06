I'm trying to use Prefect's CLI in some automation scripts, but the commands for viewing configuration settings and listing profiles only output human-readable text.

*   The 'config view' CLI command must accept a new '--output' option (long form) and '-o' option (short form). The only supported value is 'json'. If any other value is provided, the command must exit with code 1 and print: Only 'json' output format is supported.

*   When 'config view --output json' is invoked, the command must print valid JSON to stdout with a top-level 'profile' key (string containing the active profile name) and a 'settings' key (a JSON array of setting objects).

*   Each setting object in the JSON output of 'config view --output json' must include: 'name' (string), 'source' (string indicating origin such as 'env' for environment variables or 'profile' for profile-defined settings), and 'value'.

*   When 'config view --output json' is combined with '--hide-sources', the settings objects in the JSON output must NOT include a 'source' key.

*   Secret settings (such as API keys) must have their value obfuscated as '********' in the JSON output of 'config view --output json'.

*   The 'profile ls' CLI command must accept a new '--output' option (long form) and '-o' option (short form). The only supported value is 'json'. If any other value is provided, the command must exit with code 1 and print: Only 'json' output format is supported.

*   When 'profile ls --output json' is invoked, the command must print a valid JSON array to stdout. Each element must be an object with 'name' (string, the profile name) and 'active' (boolean, true if the profile is currently active, false otherwise).


*   Interface details: Type: Function
Name: view
Location: src/prefect/cli/config.py
Signature: view(..., output: str | None = None) -> None
Description: Existing CLI command that displays the current Prefect configuration settings. Must be extended with a new '--output' / '-o' CLI parameter (keyword argument 'output', default None). When output is 'json', prints a JSON object to stdout with keys 'profile' (string) and 'settings' (list of objects). Each settings object contains 'name' (string), 'value', and 'source' (string). When the existing '--hide-sources' flag is also provided, 'source' is omitted from each settings object in JSON output. Secret values must appear as '********'. When an unsupported output format is given, exit with code 1 and print "Only 'json' output format is supported.".

Type: Function
Name: ls
Location: src/prefect/cli/profile.py
Signature: ls(*, output: str | None = None) -> None
Description: Existing CLI command that lists all available Prefect profiles. Must be extended with a new '--output' / '-o' CLI parameter (keyword argument 'output', default None). When output is 'json', prints a JSON array to stdout where each element is an object with 'name' (string) and 'active' (boolean, true if the profile is currently active, false otherwise). When an unsupported output format is given, exit with code 1 and print "Only 'json' output format is supported.".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.