I'd like to add shell completion support to the nx CLI so that developers can press Tab in their terminal and get relevant suggestions for project names, target names, generator plugins, and flag values.

*   parseCompletionArgs must accept a process.argv-style string array, strip the first two entries (runtime + binary), then strip a leading 'nx' script-name token if present, and return an object with `tokens` (remaining shell tokens), `current` (last token), and `previousToken` (second-to-last token, or '' when only one token exists). It must return null when no user tokens remain.

*   DESC_SEPARATOR must be exported as the TAB character ('\t'), not a colon, because completion values and command names may themselves contain colons.

*   formatDescription must strip the '__yargsString__:' prefix from description strings, collapse any literal TAB characters to spaces, and return '' for undefined or empty input. It must leave colons that appear in the rest of the description untouched.

*   shellRendersDescriptions must read the NX_COMPLETE environment variable and return true only for 'zsh' and 'fish'. It must return false for 'bash', 'powershell', 'nushell', and when NX_COMPLETE is unset.

*   getProjectNameCompletions must return all project names from the cached project graph that start with the given prefix string. It must return [] when the project graph is unavailable.

*   getProjectNamesWithTarget must return project names matching the prefix that also have a target entry matching the given target name. It must return [] for nonexistent targets.

*   getTargetNameCompletions must return deduplicated target names across all workspace projects matching the given prefix. getTargetNamesForProject must return targets for the named project; when the project is not found, it must fall back to all workspace targets (to support partial project-name typing).

*   completeProjectTarget must implement two-stage completion: before any colon is typed it returns project names each suffixed with ':', after a colon is typed it returns 'projectName:targetName' candidates. For an unknown project name, it must use all workspace targets prefixed with the typed name.

*   getGeneratorPluginCompletions must return plugin names from both devDependencies in the root package.json and from workspace project-graph nodes, filtering to only those with a generators field. It must return [] when root package.json is missing.

*   getGeneratorsForPlugin must return non-hidden generator names for the specified plugin matching the given prefix. It must return [] when the plugin's package.json is malformed.

*   completeGenerator must implement two-stage completion: before a colon returns matching plugin names with ':' PLUS all non-hidden bare generator names from all plugins that match the prefix; after a colon returns 'pluginName:generatorName' candidates filtered by the typed portion after the colon.

*   registerCompletion must accept a space-separated command path string and a metadata object with optional positionals (each having choices and/or a complete function) and optional flags (map of flag name to completion function). findCompletionMetadata must pick the longest matching prefix, stopping at flag tokens, and return the metadata plus the positionalIndex (non-flag tokens typed beyond the matched path). findFlagCompletion must return the handler for a flag name or null. resolveCompletion must dispatch to flag handlers (with precedence over positionals), return [] for a flag with no registered handler (no fallthrough), dispatch to positional completers or filter choices, and return null for empty args, no metadata match, or positionalIndex beyond declared positionals.

*   generateScript must return a Promise resolving to a shell script for the given shell (bash, zsh, fish, or powershell). The script must contain the sentinels '###-begin-nx-completions-###' and '###-end-nx-completions-###', set NX_COMPLETE to exactly the target shell name, contain NX_VERBOSE_LOGGING and a stderr discard ('2>/dev/null' for POSIX or '2>$null' for PowerShell), include a directory walk-up loop to locate the workspace-local nx binary, and the zsh script must use 'compadd -d' and must never invoke '_describe ''.'

*   isCompletionRequest must return true when NX_COMPLETE is any non-empty string, and false when it is undefined or empty. getCompletionShell must return the shell name when NX_COMPLETE is one of 'bash', 'zsh', 'fish', or 'powershell', and null otherwise (including unrecognized values).

*   tryValueCompletion must accept a process.argv array, use parseCompletionArgs to extract tokens, call resolveCompletion, write each returned candidate as its own line to stdout via console.log when completions are found, and return true. It must return false when no tokens are found or when no metadata matches. The complete function in metadata must be called with (current, tokens) where tokens starts from the command name.

*   The registrations module must wire completion metadata for: 'run' (project:target), 'generate' and its alias 'g' sharing the same metadata object (plugin:generator plus bare names), 'show project' (project names), 'show target' (project:target candidates plus the fixed choices 'inputs' and 'outputs'), 'show target inputs' and 'show target outputs' (project:target), 'add' (prefixed @nx/* package names from the nx package group). Flag completions must be registered for run-many, affected, graph, and watch commands with all yargs-declared aliases sharing the same handler reference. Infix target completions must be registered for conventional targets (at minimum build, test, lint, serve) completing to project names that have that target.

*   getNxCommandHandlers must return a Record mapping command names (including 'run-many', 'affected', 'graph', 'watch') to handler objects with a 'builder' function. introspectBuilder must accept a yargs builder function and return an object with an 'aliases' Map from canonical option name to its declared alias array, or null on failure.


*   Interface details: Type: Function
Name: parseCompletionArgs
Location: packages/nx/src/command-line/completion/argv-layout.ts
Signature: parseCompletionArgs(argv: string[]) -> { tokens: string[]; current: string; previousToken: string } | null
Description: Parses a process.argv-style array for shell completion. Strips the first two entries (node runtime and nx binary path), then strips a leading 'nx' token if present. Returns an object with `tokens` (all remaining shell tokens), `current` (the last token, the partial string being completed), and `previousToken` (the second-to-last token, or '' if only one token remains). Returns null if no user-supplied tokens are present after stripping.

Type: Constant
Name: DESC_SEPARATOR
Location: packages/nx/src/command-line/completion/command-completions.ts
Signature: DESC_SEPARATOR: string
Description: The separator between a completion value and its description. Must be a TAB character ('\t') — not a colon — because completion values and command names may themselves contain colons (e.g. 'my-app:build').

Type: Function
Name: formatDescription
Location: packages/nx/src/command-line/completion/command-completions.ts
Signature: formatDescription(description: string | undefined) -> string
Description: Sanitizes a command description for use in shell completion output. Strips the yargs i18n marker prefix '__yargsString__:' from the start of the string. Collapses any literal TAB characters in the remaining string to spaces (to prevent breaking the value/description split in shells). Returns '' for undefined or empty input. Leaves colons in the description untouched.

Type: Function
Name: shellRendersDescriptions
Location: packages/nx/src/command-line/completion/command-completions.ts
Signature: shellRendersDescriptions() -> boolean
Description: Returns true if the current shell (determined by the NX_COMPLETE environment variable) renders completion descriptions — specifically 'zsh' and 'fish'. Returns false for 'bash', 'powershell', 'nushell', or when NX_COMPLETE is unset.

Type: Function
Name: getProjectNameCompletions
Location: packages/nx/src/command-line/completion/completion-providers.ts
Signature: getProjectNameCompletions(prefix: string) -> string[]
Description: Returns all project names from the cached project graph that start with the given prefix. Returns [] if the project graph is unavailable.

Type: Function
Name: getProjectNamesWithTarget
Location: packages/nx/src/command-line/completion/completion-providers.ts
Signature: getProjectNamesWithTarget(prefix: string, target: string) -> string[]
Description: Returns project names that start with the given prefix AND have a target matching the given target name. Returns [] if no projects match or the graph is unavailable.

Type: Function
Name: getTargetNameCompletions
Location: packages/nx/src/command-line/completion/completion-providers.ts
Signature: getTargetNameCompletions(prefix: string) -> string[]
Description: Returns unique target names across all workspace projects that start with the given prefix. Returns [] if graph is unavailable.

Type: Function
Name: getTargetNamesForProject
Location: packages/nx/src/command-line/completion/completion-providers.ts
Signature: getTargetNamesForProject(prefix: string, projectName: string) -> string[]
Description: Returns target names for a specific project that start with the given prefix. If the project name does not exist in the graph, falls back to all workspace target names (to support partial project-name typing scenarios like 'project:t<TAB>').

Type: Function
Name: completeProjectTarget
Location: packages/nx/src/command-line/completion/completion-providers.ts
Signature: completeProjectTarget(current: string) -> string[]
Description: Two-stage completion for 'project:target' tokens. Stage 1 (no colon in current): returns matching project names each suffixed with ':'. Stage 2 (colon present in current): returns 'projectName:targetName' candidates matching the portion after the colon, using all workspace targets if the project name is unknown.

Type: Function
Name: getGeneratorPluginCompletions
Location: packages/nx/src/command-line/completion/completion-providers.ts
Signature: getGeneratorPluginCompletions(prefix: string) -> string[]
Description: Returns plugin names (from devDependencies in the root package.json AND from project-graph nodes) that declare a generators field and whose name starts with the given prefix. Returns [] if root package.json is missing.

Type: Function
Name: getGeneratorsForPlugin
Location: packages/nx/src/command-line/completion/completion-providers.ts
Signature: getGeneratorsForPlugin(pluginName: string, prefix: string) -> string[]
Description: Returns generator names for a plugin that are not marked hidden and whose name starts with the given prefix. Searches node_modules and workspace-local paths. Returns [] if the plugin's package.json is malformed or missing.

Type: Function
Name: completeGenerator
Location: packages/nx/src/command-line/completion/completion-providers.ts
Signature: completeGenerator(current: string) -> string[]
Description: Two-stage generator completion. Stage 1 (no colon): returns matching plugin names suffixed with ':' PLUS all non-hidden bare generator names across all plugins whose name matches the prefix. Stage 2 (colon present): returns 'pluginName:generatorName' candidates matching the typed prefix after the colon.

Type: Function
Name: registerCompletion
Location: packages/nx/src/command-line/completion/metadata.ts
Signature: registerCompletion(path: string, metadata: { positionals?: Array<{ choices?: string[]; complete?: (current: string, args: string[]) => string[] }>; flags?: Record<string, (current: string, args: string[]) => string[]> }) -> void
Description: Registers completion metadata for a command path. The path is a space-separated string of command tokens (e.g. 'show target'). Metadata describes positional completions (via static choices or a dynamic complete function) and flag-value completions (keyed by flag name).

Type: Function
Name: findCompletionMetadata
Location: packages/nx/src/command-line/completion/metadata.ts
Signature: findCompletionMetadata(tokens: string[]) -> { metadata: object; positionalIndex: number } | null
Description: Searches the registry for the longest registered path that matches a prefix of the given tokens array. Flags (tokens starting with '--') halt the prefix walk. Returns the matching metadata and the positionalIndex (count of non-flag tokens typed beyond the matched path). Returns null when no path matches.

Type: Function
Name: findFlagCompletion
Location: packages/nx/src/command-line/completion/metadata.ts
Signature: findFlagCompletion(metadata: object | null, flag: string) -> ((current: string, args: string[]) => string[]) | null
Description: Looks up the flag completion handler for a given flag name in a metadata object. Returns null if metadata is null or the flag has no registered handler.

Type: Function
Name: resolveCompletion
Location: packages/nx/src/command-line/completion/metadata.ts
Signature: resolveCompletion(tokens: string[], current: string, previousToken: string) -> string[] | null
Description: Resolves completion candidates. If previousToken starts with '--' and a flag handler is registered, calls the handler with (current, tokens) and returns the result. If previousToken starts with '--' but no handler is registered, returns [] (no fallthrough to positionals). Otherwise dispatches to the positional completer at the computed positionalIndex: calls complete(current, tokens) or filters choices by prefix. Returns null for empty tokens, no metadata match, or positionalIndex beyond declared positionals. Flag dispatch takes precedence over positional dispatch.

Type: Function
Name: generateScript
Location: packages/nx/src/command-line/completion/scripts.ts
Signature: generateScript(shell: 'bash' | 'zsh' | 'fish' | 'powershell') -> Promise<string>
Description: Generates a shell completion installation script for the given shell. The script must contain '###-begin-nx-completions-###' and '###-end-nx-completions-###' sentinel comments. It must set the NX_COMPLETE environment variable to exactly the target shell name (POSIX: 'NX_COMPLETE=<shell>'; PowerShell: '$env:NX_COMPLETE = ''<shell>'''). It must contain 'NX_VERBOSE_LOGGING' and a conditional stderr discard ('2>/dev/null' for POSIX shells, '2>$null' for PowerShell). For bash/zsh/fish the script must include a 'while' loop that walks up from the current directory to find 'node_modules/.bin/nx' and '.nx/installation/node_modules/.bin/nx'. For PowerShell the same walk-up must find 'node_modules\.bin\nx.cmd' and '.nx\installation\node_modules\.bin\nx.cmd'. The zsh script must use 'compadd -d' and must NOT invoke '_describe '''.

Type: Function
Name: isCompletionRequest
Location: packages/nx/src/command-line/completion/trigger.ts
Signature: isCompletionRequest() -> boolean
Description: Returns true when the NX_COMPLETE environment variable is set to any non-empty string, indicating the process was invoked for shell completion. Returns false when NX_COMPLETE is undefined or an empty string.

Type: Function
Name: getCompletionShell
Location: packages/nx/src/command-line/completion/trigger.ts
Signature: getCompletionShell() -> 'bash' | 'zsh' | 'fish' | 'powershell' | null
Description: Returns the shell name from NX_COMPLETE if it is one of the four recognized shells ('bash', 'zsh', 'fish', 'powershell'). Returns null if NX_COMPLETE is unset or contains an unrecognized value (e.g. 'nushell').

Type: Function
Name: tryValueCompletion
Location: packages/nx/src/command-line/completion/value-completions.ts
Signature: tryValueCompletion(argv: string[]) -> boolean
Description: Fast-path completion entry point. Parses argv using parseCompletionArgs. If no tokens are found, returns false. Calls resolveCompletion with the parsed tokens, current, and previousToken. If resolveCompletion returns null (no registered metadata matches), returns false. If resolveCompletion returns a string array (possibly empty), writes each candidate followed by a newline to stdout via console.log and returns true.

Type: Module (side-effect)
Name: registrations
Location: packages/nx/src/command-line/completion/registrations.ts
Description: Side-effect module that calls registerCompletion for every supported nx command path. Must register: 'run' (positional 0: completeProjectTarget), 'generate' and alias 'g' sharing the same metadata object (positional 0: completeGenerator), 'show project' (positional 0: getProjectNameCompletions), 'show target' (positional 0: project:target candidates plus the fixed choices 'inputs' and 'outputs'), 'show target inputs' and 'show target outputs' (positional 0: completeProjectTarget), 'add' (positional 0: @nx/* package names from nx-migrations.packageGroup), flag completions for run-many (-p/--projects: project names, -t/--target/--targets: target names), affected (-p/--projects, --exclude: project names; -t/--target/--targets: target names), graph (--focus/--exclude: project names; -t/--target/--targets: target names), watch (-p/--projects: project names). All yargs-declared option aliases for a given canonical option must share the same handler function reference. Also registers infix target completions for conventional target names (including at minimum 'build', 'test', 'lint', 'serve') so that 'nx <target> <TAB>' completes project names with that target.

Type: Function
Name: getNxCommandHandlers
Location: packages/nx/src/command-line/completion/command-handlers.ts
Signature: getNxCommandHandlers() -> Record<string, { builder?: (yargs: Argv) => Argv }>
Description: Returns a map from command name to its yargs handler object (which includes a 'builder' function). Used by the alias-consistency tests to introspect which options each command declares.

Type: Function
Name: introspectBuilder
Location: packages/nx/src/command-line/completion/command-handlers.ts
Signature: introspectBuilder(builder: (yargs: Argv) => Argv) -> { aliases: Map<string, string[]> } | null
Description: Runs a yargs builder function in dry-run mode and extracts option alias groups. Returns an object whose 'aliases' Map maps each canonical option name to the array of yargs-declared aliases for that option. Returns null on failure.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.