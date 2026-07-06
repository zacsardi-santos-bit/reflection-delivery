I'm trying to build an agent that can operate in different phases — for example, first spending time planning and seeking user approval, then switching to a more autonomous execution phase.

*   The constant DEFAULT_MODE_SOURCE_ID must equal the string 'agent_mode', which is the key used in session state to store the current mode as a dict with a 'current_mode' field.

*   get_agent_mode must be decorated with the HARNESS experimental feature marker: its __feature_id__ attribute must equal ExperimentalFeature.HARNESS.value, and its docstring must contain '.. warning:: Experimental'.

*   set_agent_mode must be decorated with the HARNESS experimental feature marker: its __feature_id__ attribute must equal ExperimentalFeature.HARNESS.value, and its docstring must contain '.. warning:: Experimental'.

*   AgentModeProvider must be decorated with the HARNESS experimental feature marker: its __feature_id__ attribute must equal ExperimentalFeature.HARNESS.value, and its docstring must contain '.. warning:: Experimental'.

*   get_agent_mode(session) called on a fresh session must return 'plan' and initialize session.state[DEFAULT_MODE_SOURCE_ID] to {'current_mode': 'plan'}. The function must accept optional keyword arguments: default_mode, available_modes, and source_id.

*   get_agent_mode must raise TypeError (message matching the regex pattern "source_id 'agent_mode'.*str") if the existing value at the source_id key in session.state is not a dict, without overwriting it.

*   get_agent_mode must fall back to the default mode when the stored mode is not in the available_modes iterable, and must also reset session.state[source_id]['current_mode'] to that default.

*   When available_modes is provided but default_mode is not, get_agent_mode must use the first element of available_modes as the default.

*   set_agent_mode must strip whitespace and lowercase the mode argument before validating and storing it, and must return the normalized (lowercase) string. It must raise ValueError (message matching 'Invalid mode') for mode values not in the available set.

*   AgentModeProvider() constructed with no arguments must default to modes 'plan' and 'execute'.

*   AgentModeProvider(mode_descriptions={}) must raise ValueError (message matching 'at least one mode'). AgentModeProvider(default_mode='ship') must raise ValueError (message matching 'Invalid mode').

*   When default_mode is omitted, AgentModeProvider must use the first key in mode_descriptions (lowercased) as the default; the provider's default_mode property must return this lowercased value.

*   When AgentModeProvider is used in a session, it must inject instructions containing: '## Agent Mode', 'Use the set_mode tool to switch between modes as your work progresses.', and 'You are currently operating in the {mode} mode.' (with the mode name lowercased, e.g., 'You are currently operating in the plan mode.'). For the default 'plan' mode the instructions must also contain 'ask clarifying questions, discuss options, and get user approval before proceeding'; for 'execute' mode: 'If you encounter ambiguity, choose the most reasonable option and note your choice'.

*   For custom modes, AgentModeProvider instructions must include each mode name with its description in the format '"<ModeName>": <description>' (quoted display name as given in mode_descriptions, colon, then description text), e.g., '"Draft": Draft it.'

*   AgentModeProvider must inject two tools named 'get_mode' and 'set_mode' into the agent session. The get_mode tool's invoke() result must have result[0].text as valid JSON deserializing to {"mode": <current_mode_normalized_lowercase>}. The set_mode tool's invoke(arguments={"mode": <mode_name>}) result must have result[0].text as valid JSON deserializing to {"mode": <normalized_lowercase_mode>, "message": "Mode changed to '<normalized_lowercase_mode>'."}. Both tools return the lowercase-normalized (stripped + lowercased) form of the mode name.


*   Interface details: Type: Constant
Name: DEFAULT_MODE_SOURCE_ID
Location: python/packages/core/agent_framework/_harness/_mode.py
Description: String constant equal to "agent_mode". Used as the default key in session.state for storing mode state (a dict with "current_mode" field). Must be exported from python/packages/core/agent_framework/__init__.py.

---

Type: Function
Name: get_agent_mode
Location: python/packages/core/agent_framework/_harness/_mode.py
Signature: get_agent_mode(session: AgentSession, *, default_mode: str | None = None, available_modes: tuple[str, ...] | None = None, source_id: str = DEFAULT_MODE_SOURCE_ID) -> str
Description: Returns the current agent mode (lowercase normalized) from session state. Initializes state if not present. Raises TypeError (message matching the pattern "source_id 'agent_mode'.*str") if state at the source_id key is not a dict. Falls back to default_mode when the stored mode is not in available_modes, also resetting the stored value. When default_mode is None and available_modes is provided, uses the first element as the default. Must have __feature_id__ == ExperimentalFeature.HARNESS.value and docstring containing ".. warning:: Experimental". Must be exported from python/packages/core/agent_framework/__init__.py.

---

Type: Function
Name: set_agent_mode
Location: python/packages/core/agent_framework/_harness/_mode.py
Signature: set_agent_mode(session: AgentSession, mode: str, *, source_id: str = DEFAULT_MODE_SOURCE_ID, available_modes: tuple[str, ...] | None = None) -> str
Description: Strips whitespace and lowercases mode, validates it against available_modes (raises ValueError matching "Invalid mode" if invalid), stores it in session.state[source_id]["current_mode"], and returns the normalized (lowercase) mode string. Must have __feature_id__ == ExperimentalFeature.HARNESS.value and docstring containing ".. warning:: Experimental". Must be exported from python/packages/core/agent_framework/__init__.py.

---

Type: Class
Name: AgentModeProvider
Location: python/packages/core/agent_framework/_harness/_mode.py
Description: Context provider that manages agent mode state and injects mode-related instructions and tools into the agent session. Must extend ContextProvider. Must have __feature_id__ == ExperimentalFeature.HARNESS.value and docstring containing ".. warning:: Experimental". Must be exported from python/packages/core/agent_framework/__init__.py.

Constructor signature: AgentModeProvider(source_id: str = DEFAULT_MODE_SOURCE_ID, *, default_mode: str | None = None, mode_descriptions: dict[str, str] | None = None, instructions: str | None = None)
- Raises ValueError (matching "at least one mode") if mode_descriptions is an empty dict.
- Raises ValueError (matching "Invalid mode") if default_mode is not in the configured modes.
- Default construction (no arguments) uses modes "plan" and "execute".
- When default_mode is omitted and mode_descriptions is provided, uses the first key (lowercased) as the default.

Property: default_mode -> str
- Returns the default mode (lowercased).

Property: source_id -> str
- Returns the source identifier used as the session state key.

Session preparation behavior (via before_run or equivalent):
- Injects instructions that must contain:
  - "## Agent Mode"
  - "Use the set_mode tool to switch between modes as your work progresses."
  - "You are currently operating in the {current_mode} mode." (mode name lowercased, e.g., "You are currently operating in the plan mode.")
  - For default "plan" mode: "ask clarifying questions, discuss options, and get user approval before proceeding"
  - For default "execute" mode: "If you encounter ambiguity, choose the most reasonable option and note your choice"
  - For custom modes: entries in the format '"<DisplayModeName>": <description>' (quoted display name, colon, description text), e.g., '"Draft": Draft it.'
- Injects a tool named "get_mode":
  - invoke() -> list where result[0].text is valid JSON deserializing to {"mode": <current_mode_normalized_lowercase>}
- Injects a tool named "set_mode":
  - invoke(arguments={"mode": <mode_name>}) -> list where result[0].text is valid JSON deserializing to {"mode": <normalized_lowercase_mode>, "message": "Mode changed to '<normalized_lowercase_mode>'."}
  - Both tool responses return the lowercase-normalized (stripped + lowercased) form of the mode name, NOT the original display casing.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.