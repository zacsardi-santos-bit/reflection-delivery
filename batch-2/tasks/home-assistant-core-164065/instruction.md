I'm working on the Anthropic integration for Home Assistant and want to add support for two server-side AI capabilities: executing shell commands and reading/editing files in a sandboxed environment.

*   Must add a constant named CONF_CODE_EXECUTION with string value 'code_execution' to homeassistant/components/anthropic/const.py, and include a default value of False for this constant in the DEFAULT dictionary.

*   The configuration flow for conversation and AI task subentries must include CONF_CODE_EXECUTION as a boolean option. When this option is absent from an existing subentry's data, it must default to False.

*   All Anthropic API message creation calls must include a 'container' keyword argument. When no active container exists for the conversation, this value must be None. When an active container exists (from a previous code execution turn in the same conversation), the container's ID string must be passed.

*   When the model uses a bash code execution server tool, the conversation must record a tool call entry with 'external' set to True, 'tool_name' set to 'bash_code_execution', and 'tool_args' containing the parsed command arguments (e.g., {'command': '...'}).

*   When a bash code execution result block is received: on success, the tool_result dict must have 'type': 'bash_code_execution_result' with 'content', 'return_code', 'stderr', and 'stdout' fields. On error, it must have 'type': 'bash_code_execution_tool_result_error' with an 'error_code' field.

*   When the model uses a text editor code execution server tool, the conversation must record a tool call entry with 'external' set to True, 'tool_name' set to 'text_editor_code_execution', and 'tool_args' containing the parsed command arguments.

*   When a text editor code execution result block is received, the tool_result dict type must match the operation: 'text_editor_code_execution_create_result' (with 'is_file_update'), 'text_editor_code_execution_str_replace_result' (with 'lines', 'new_lines', 'new_start', 'old_lines', 'old_start'), 'text_editor_code_execution_view_result' (with 'content', 'file_type', 'num_lines', 'start_line', 'total_lines'), or 'text_editor_code_execution_tool_result_error' (with 'error_code' and 'error_message').

*   The native data field of assistant messages must include a 'container' field. When code execution tools are active, this must contain the Container object (with 'id' and 'expires_at') returned in the stream's message delta. When no code execution occurred, this field must be None.

*   When a conversation turn results in a container being assigned, subsequent turns in the same conversation must pass that container's ID string as the 'container' kwarg to the API. The container ID is extracted from the previous assistant message's native container field.

*   Both bash_code_execution and text_editor_code_execution server tool use blocks must be correctly serialized back into the API message history when converting the chat log to Anthropic API message format for multi-turn conversations.


*   Interface details: Type: Constant
Name: CONF_CODE_EXECUTION
Location: homeassistant/components/anthropic/const.py
Signature: CONF_CODE_EXECUTION: str = "code_execution"
Description: Configuration key for the code execution feature toggle. Must be the string "code_execution". Must also have a corresponding entry in the DEFAULT dictionary with value False.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.