I'm working on improving how DSPy model traces are displayed in the model trace explorer.

*   The formatDspySections function must convert standalone DSPy section markers (lines that consist only of `[[ ## name ## ]]`, optionally surrounded by leading/trailing whitespace) into markdown level-4 headings of the form `#### Name`.

*   The formatDspySections function must remove the `[[ ## completed ## ]]` marker entirely — it must not be converted to a heading; the line becomes empty in the output.

*   The formatDspySections function must preserve markers that appear inside backtick inline code spans (e.g., `[[ ## reasoning ## ]]`) completely unchanged.

*   The formatDspySections function must title-case snake_case marker names: underscores are replaced with spaces and each word is capitalized (e.g., `tool_name_0` becomes `Tool Name 0`).

*   The formatDspySections function must return the input string unchanged when it contains no DSPy markers, and must return an empty string unchanged when given empty string input.

*   The normalizeConversation function in ModelTraceExplorer.utils.ts, when called in DSPy mode with message input data, must apply section formatting to each message's content so that standalone markers become markdown headings, the completed marker is removed, and markers inside backtick inline code are preserved.

*   The normalizeConversation function, when called in DSPy mode with an array containing a single JSON string as output, must return a single assistant-role message whose content is the JSON rendered as a fenced code block: the string '```json\n' followed by the JSON pretty-printed with 2-space indentation, followed by '\n```'.


*   Interface details: Type: Function
Name: formatDspySections
Location: mlflow/server/js/src/shared/web-shared/model-trace-explorer/chat-utils/dspy.ts
Signature: formatDspySections(input: string): string
Description: Converts DSPy section markers of the form `[[ ## name ## ]]` that appear standalone on a line (optionally with leading/trailing whitespace) into markdown headings of the form `#### Name`. The special "completed" marker (`[[ ## completed ## ]]`) is removed entirely from the output rather than converted to a heading. Markers that appear inside backtick inline code spans (e.g., `[[ ## name ## ]]`) are left completely unchanged. Snake_case marker names are title-cased with underscores replaced by spaces (e.g., `tool_name_0` becomes `Tool Name 0`). Empty string input returns empty string; input without any markers is returned unchanged. Must be exported from the module.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.