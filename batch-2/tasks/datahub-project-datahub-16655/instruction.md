I'm working on the DataHub metadata ingestion library and I'd like to add automatic detection of what tool or environment is invoking the CLI at runtime.

*   The identify_caller function must be decorated with functools.lru_cache (maxsize=1), making it cache its result after the first call. Subsequent calls must return the cached result even if environment variables have changed, and it must expose a cache_clear() method.

*   identify_caller must never raise an exception. If any internal error occurs during detection, it must return the string 'unknown'.

*   identify_caller must return a string with length less than 200, containing no newline (\n) or carriage return (\r) characters.

*   identify_caller must check the DATAHUB_CALLER environment variable first. If set, its value must be returned immediately, taking precedence over all other detection methods including GITHUB_ACTIONS and other CI/tool signals.

*   identify_caller must detect these environment variable signals in order of priority: CLAUDECODE (any truthy value) → 'claude-code'; CURSOR_TRACE_ID (any value) → 'cursor'; LANGCHAIN_TRACING_V2 (any truthy value) → 'langchain'; LANGSMITH_API_KEY (any value) → 'langchain'; LANGGRAPH_API_URL (any value) → 'langgraph'; GITHUB_ACTIONS (any truthy value) → 'github-actions'; JENKINS_URL (any value) → 'jenkins'; CI with value 'true', '1', or 'yes' (case-insensitive) → 'ci'.

*   _CALLER_SIGNATURES must be a dictionary (or dict-like) that maps environment variable signatures to caller label strings. It must be importable by name from the module. Iterating over it must yield the signature keys. It must contain at minimum: CLAUDECODE, CURSOR_TRACE_ID, LANGCHAIN_TRACING_V2, LANGSMITH_API_KEY, LANGGRAPH_API_URL, GITHUB_ACTIONS, JENKINS_URL. All signature keys are plain env var names (no '=' suffix) for key-presence matching, except any that require exact key=value matching. AI coding tool signatures (CLAUDECODE, CURSOR_TRACE_ID, etc.) must appear before CI system signatures (GITHUB_ACTIONS, JENKINS_URL, etc.) in the dictionary, because when _match_signatures scans a parent env string containing multiple matching signatures, it returns the first match.

*   _match_signatures must accept a string of environment variable text (space- or newline-separated key=value pairs). For each key in _CALLER_SIGNATURES that does NOT contain '=', it must check whether the string f'{key}=' appears in the text (key-presence match). If a signature key DOES contain '=', it must check for an exact substring match of the full 'KEY=value' string. It must return the label of the first matching signature, or None if none match.

*   _read_parent_env_linux must accept an integer PID, read /proc/<pid>/environ, replace all null bytes (\0) with newlines (\n), and return the resulting string. It must return None on PermissionError or FileNotFoundError.

*   _read_parent_env_macos must accept an integer PID, run a subprocess to retrieve the parent process environment, and return the stdout string if the command exits with code 0. It must return None if the exit code is nonzero or if subprocess.TimeoutExpired is raised.

*   _get_process_name must accept an integer PID and return the basename of the process command name (e.g., '/usr/bin/zsh' → 'zsh'). If the name is 'java', it must additionally check the full command line; if the full command contains the string 'GradleDaemon', return 'gradle' instead of 'java'. Must return None on nonzero subprocess exit or TimeoutExpired.

*   _get_full_command must accept an integer PID and return the full command line string with leading/trailing whitespace stripped. Must return None on nonzero subprocess exit or TimeoutExpired.

*   _get_parent_pid must accept an integer PID and return the integer parent PID. Must return None for PID 1 (init). Must return None on nonzero subprocess exit, TimeoutExpired, or non-integer output.

*   _get_ancestor_chain must accept a max_depth integer parameter (default 4) and return a list of process name strings, walking the process tree from the current process's parent upward. It must stop and return early when _get_process_name returns None, when _get_parent_pid returns None, or when max_depth entries have been collected.

*   identify_caller must implement parent process environment detection (Tier 3): on Linux (platform.system() == 'Linux'), use _read_parent_env_linux with the parent PID; on macOS/Darwin, use _read_parent_env_macos. If the resulting env string matches a signature, return the corresponding label.

*   identify_caller must implement process tree heuristics (Tier 4) when Tier 3 produces no match: if the ancestor chain contains 'cursor' (case-insensitive), return 'cursor'; if it contains 'claude', return 'claude-code'; if the first process in the chain is a shell (bash, zsh, fish, sh, dash, tcsh, or ksh), return 'terminal'; if the chain has an unknown first process, return that process name; if the chain is empty, return 'unknown'.

*   The _get_user_agent_string method of RequestsSessionConfig must embed the caller label by appending '/<caller>' to the component name. The resulting User-Agent string must match the format: DataHub-Client/1.0 (<mode>; <component>/<caller>; <version>). For example, with datahub_component='datahub' and DATAHUB_CALLER='test-tool', the string must contain 'datahub/test-tool' and match the regex r"DataHub-Client/1\.0 \(\w+; datahub/test-tool; .+\)".


*   Interface details: Type: Module
Name: caller_context
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Description: New module that auto-detects what tool or environment invoked the DataHub CLI, using a tiered approach: explicit env var override, own environment signatures, parent process environment, and process-tree heuristics.

Type: Variable
Name: _CALLER_SIGNATURES
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Description: Dictionary mapping environment variable signatures to caller label strings. Iterable; keys are either bare env var names (key-presence match) or "KEY=value" strings (exact-match). Must contain at minimum the following entries (in this order, AI coding tools before CI systems):
  - "CLAUDECODE" → "claude-code"
  - "CURSOR_TRACE_ID" → "cursor"
  - "LANGCHAIN_TRACING_V2" → "langchain"
  - "LANGSMITH_API_KEY" → "langchain"
  - "LANGGRAPH_API_URL" → "langgraph"
  - "GITHUB_ACTIONS" → "github-actions"
  - "JENKINS_URL" → "jenkins"
The ordering is significant: when multiple signatures are present in a parent environment string, the first matching entry wins.

Type: Function
Name: identify_caller
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: identify_caller() -> str
Description: Returns a short label string identifying the tool that invoked the CLI. Decorated with functools.lru_cache(maxsize=1), so it exposes a cache_clear() method. Detection priority:
  1. DATAHUB_CALLER env var (explicit override, takes highest priority)
  2. Known env var signatures in the current process environment
  3. CI=true/1/yes → "ci"
  4. Parent process environment (Linux: /proc, macOS: ps eww) matched against _CALLER_SIGNATURES
  5. Process tree heuristics (ancestor chain)
  Never raises exceptions; returns "unknown" on any internal error. Return value is always a string with len < 200 and no newline characters.

Type: Function
Name: _identify_caller_inner
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: _identify_caller_inner() -> str
Description: Internal helper called by identify_caller(). Performs the actual tiered detection. Wrapped by identify_caller() which catches all exceptions and returns "unknown".

Type: Function
Name: _match_signatures
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: _match_signatures(env_text: str) -> Optional[str]
Description: Checks a text string (newline- or space-separated environment variables) for known caller signatures from _CALLER_SIGNATURES. For signatures without "=", checks if f"{key}=" is present in env_text (key-presence match). For signatures with "=", checks if the full "KEY=value" string is present. Returns the label of the first matching signature, or None if no match.

Type: Function
Name: _read_parent_env_linux
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: _read_parent_env_linux(ppid: int) -> Optional[str]
Description: Reads the environment of process ppid from /proc/<ppid>/environ. Replaces null bytes (\0) with newlines (\n) to produce a newline-separated env string. Returns None on PermissionError or FileNotFoundError.

Type: Function
Name: _read_parent_env_macos
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: _read_parent_env_macos(ppid: int) -> Optional[str]
Description: Retrieves the parent process environment on macOS by running a ps subprocess command. Returns stdout string if the command exits with code 0. Returns None if the exit code is nonzero or if subprocess.TimeoutExpired is raised.

Type: Function
Name: _get_process_name
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: _get_process_name(pid: int) -> Optional[str]
Description: Returns the command basename for a given PID (e.g. "/usr/bin/zsh" → "zsh"). Special case: if the name is "java" and the full command contains "GradleDaemon", returns "gradle". Returns None if the subprocess exits nonzero or raises TimeoutExpired.

Type: Function
Name: _get_full_command
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: _get_full_command(pid: int) -> Optional[str]
Description: Returns the full command line for a given PID, with leading/trailing whitespace stripped. Returns None if the subprocess exits nonzero or raises TimeoutExpired.

Type: Function
Name: _get_parent_pid
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: _get_parent_pid(pid: int) -> Optional[int]
Description: Returns the integer parent PID for a given PID. Returns None for PID 1 (init — treated as end of chain). Returns None if the subprocess exits nonzero, raises TimeoutExpired, or if the output is not a valid integer.

Type: Function
Name: _get_ancestor_chain
Location: metadata-ingestion/src/datahub/utilities/caller_context.py
Signature: _get_ancestor_chain(max_depth: int = 4) -> List[str]
Description: Walks the process tree starting from the current process's parent (os.getppid()), collecting process names via _get_process_name. Stops when _get_process_name returns None, _get_parent_pid returns None, or max_depth entries have been collected. Returns a list of process name strings.

Type: Class
Name: RequestsSessionConfig
Location: metadata-ingestion/src/datahub/emitter/rest_emitter.py
Description: Existing config model for HTTP session configuration. The _get_user_agent_string method must be updated to embed the caller label.
Signature: _get_user_agent_string(session) -> str
  Returns a User-Agent string in the format:
    DataHub-Client/1.0 (<mode>; <component>/<caller>; <version>)
  where <caller> is the result of identify_caller() and <component> is the datahub_component field.
  The string must match the regex: r"DataHub-Client/1\.0 \(\w+; <component>/<caller>; .+\)"


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.