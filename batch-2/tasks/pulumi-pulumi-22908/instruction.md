I'm working on enriching the HTTP user-agent string that the Pulumi CLI sends to the Pulumi Cloud backend.

*   The UserAgent function in the httpstate client package must return a string matching the pattern '^pulumi-cli/1 \([^()]*\)$'. When no command or AI agent has been set, the returned string must not contain 'cmd=' or 'agent='.

*   SetUserAgentCommand must be an exported function in the httpstate client package that accepts a command string and stores it as package-level state. When set to a non-empty value, subsequent calls to UserAgent must include '; cmd=<sanitized-value>' within the parenthesized section. Setting it to an empty string must remove the 'cmd=' segment from UserAgent output.

*   SetUserAgentAIAgent must be an exported function in the httpstate client package that accepts an AI agent name string and stores it as package-level state. When set to a non-empty value, subsequent calls to UserAgent must include '; agent=<value>' as the last token before the closing parenthesis. Setting it to an empty string must remove the 'agent=' segment from UserAgent output.

*   When both command and AI agent are set, UserAgent must include both fields: the command appears as '; cmd=<value>;' (with a trailing semicolon since agent follows), and the AI agent appears as '; agent=<value>)' at the end. The command must appear before the agent in the User-Agent string.

*   SetUserAgentCommand must sanitize the input value: leading and trailing whitespace is stripped, spaces and tabs are replaced with hyphens, and parentheses '(' ')' and semicolons ';' are removed. For example, 'weird (cmd; with) bits' must become 'weird-cmd-with-bits'.

*   DetectAIAgent must be an exported function (capitalized) in the pkg/cmd/pulumi/metadata package with the signature DetectAIAgent(getEnv func(string) string) string. It was previously unexported as detectAIAgent and must be renamed to be accessible from other packages.

*   DetectAIAgent must detect AI coding environments from well-known environment variables, including those for Copilot (COPILOT_MODEL, COPILOT_ALLOW_ALL, COPILOT_GITHUB_TOKEN), Codex (CODEX_THREAD_ID, CODEX_SANDBOX, CODEX_CI), Cursor (CURSOR_TRACE_ID, CURSOR_AGENT), Gemini CLI (GEMINI_CLI), Antigravity (ANTIGRAVITY_AGENT), Augment (AUGMENT_AGENT), OpenCode (OPENCODE, OPENCODE_CALLER, OPENCODE_CLIENT), Claude Code with cowork mode (CLAUDE_CODE_IS_COWORK taking precedence over CLAUDECODE/CLAUDE_CODE), Replit (REPL_ID), and Goose (GOOSE_PROVIDER). An explicit AI_AGENT environment variable must take precedence over all auto-detected values.


*   Interface details: Type: Function
Name: SetUserAgentCommand
Location: pkg/backend/httpstate/client/api.go
Signature: SetUserAgentCommand(command string)
Description: Sets the CLI command to be appended to the User-Agent header as "cmd=<sanitized-value>". The input is sanitized: leading/trailing whitespace is stripped, spaces and tabs are replaced with hyphens, and parentheses and semicolons are removed. Setting to an empty string removes the "cmd=" segment from the User-Agent.

Type: Function
Name: SetUserAgentAIAgent
Location: pkg/backend/httpstate/client/api.go
Signature: SetUserAgentAIAgent(agent string)
Description: Sets the AI agent name to be appended to the User-Agent header as "agent=<value>". Setting to an empty string removes the "agent=" segment from the User-Agent.

Type: Function
Name: DetectAIAgent
Location: pkg/cmd/pulumi/metadata/metadata.go
Signature: DetectAIAgent(getEnv func(string) string) string
Description: Exported version of the previously unexported detectAIAgent function. Returns the name of the AI coding environment inferred from well-known environment variables, or an empty string if none is detected. The getEnv parameter is a function used to look up environment variable values (e.g., os.Getenv).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.