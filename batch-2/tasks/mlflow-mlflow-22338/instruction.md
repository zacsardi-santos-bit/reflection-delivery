I'm building a TypeScript integration that bridges Claude Code session transcripts with MLflow's tracing system.

*   readTranscript must read a JSONL file line by line, parse each non-empty line as JSON, and return an array of TranscriptEntry objects in order.

*   parseTimestampToNs must return null for null, undefined, and empty string inputs. It must also return null for strings that are not valid ISO date strings.

*   parseTimestampToNs must convert an ISO date string to nanoseconds by parsing it as a Date and scaling to ns. For numeric inputs: values >= 1e13 are treated as nanoseconds and returned as Math.floor(ts); values >= 1e10 are treated as milliseconds and returned as Math.floor(ts * 1e6); smaller values are treated as Unix seconds and returned as Math.floor(ts * 1e9).

*   extractTextContent must return a string argument unchanged. For an array of content blocks, it must join the text fields of all blocks whose type is 'text' with a newline character '\n'. An empty array must return an empty string.

*   findLastUserMessageIndex must return null for an empty entries array. It must return the index of the last user transcript entry that is a real user message — skipping entries whose message content is an array of tool_result blocks, entries with isCompactSummary set to true, and user entries whose text content starts with 'Base directory:' (skill injection messages).

*   findFinalAssistantResponse must search the entries array from startIndex onward and return the extracted text content of the last assistant entry that has text content. It must return null if no such entry exists.

*   processTranscript must create a root span of type AGENT named 'claude_code_conversation' with inputs { prompt: <first user message text> } and outputs { status: 'completed', response: <last assistant text response> }.

*   processTranscript must create one LLM span per assistant turn that contains message content. Each LLM span's inputs must include a messages array, its outputs must be in Anthropic message format ({ type: 'message', role: 'assistant', content: [...] }), and it must have the attribute mlflow.message.format set to 'anthropic'. LLM spans must have startTimeNs and endTimeNs derived from transcript timestamps.

*   processTranscript must create one TOOL span per tool use, named 'tool_<ToolName>'. The span's inputs must equal the tool's input object, its outputs must be { result: <tool result content> }, and it must have attributes tool_name (the tool name string) and tool_id (the tool use id string). All TOOL and LLM spans must be children of the root span.

*   processTranscript must set token usage on LLM spans as the attribute mlflow.chat.tokenUsage. The input_tokens value must equal input_tokens + cache_creation_input_tokens (cache_read_input_tokens is excluded). total_tokens must equal the adjusted input_tokens plus output_tokens.

*   processTranscript must set the following trace metadata: mlflow.trace.session = the sessionId argument; mlflow.trace.user = process.env.USER or empty string; mlflow.trace.working_directory = process.cwd(); mlflow.trace.permission_mode = the permissionMode field from the first user entry that has one; mlflow.claude_code_version = the version field from transcript entries that have one.

*   processTranscript must set the trace info requestPreview to the first real user message text and responsePreview to the final assistant text response.

*   processTranscript must call flushTraces after processing the transcript.

*   processTranscript must handle sub-agents with inline progress events: when a 'progress' entry references a parent tool_use of type Task, create a nested AGENT span named 'subagent_<subagent_type>' as a child of the tool_Task span, and populate its children (LLM and TOOL spans) from the progress messages.

*   processTranscript must handle sub-agents stored in separate files: when a tool result includes an agentId and a corresponding file exists at <transcript_dir>/<transcript_basename>/subagents/agent-<agentId>.jsonl, read that file and create the same nested AGENT span hierarchy (named 'subagent_<subagent_type>') as a child of the tool_Task span.

*   processTranscript must create one tool_Task span for each parallel tool use call in a single assistant turn, and each span's outputs must be set from the corresponding tool result.

*   processTranscript must record an exception on a TOOL span when the corresponding tool result has is_error set to true. The exception message must contain the phrase "doesn't want to proceed".

*   processTranscript must create no spans (must not call startSpan) when given an empty transcript file, a transcript with no real user messages, or a path to a file that does not exist.

*   processTranscript must include queue-operation entries with operation 'enqueue' as user messages (with the entry's content as the message text) in the inputs of the subsequent LLM span. Queue-operation entries with other operation types must be ignored.


*   Interface details: Type: Function
Name: processTranscript
Location: libs/typescript/integrations/claude-code/src/tracing.ts
Signature: processTranscript(filePath: string, sessionId: string): Promise<void>
Description: Reads a Claude Code JSONL transcript file and converts it into an MLflow trace with a nested span hierarchy. Creates an AGENT root span named 'claude_code_conversation', LLM spans for each assistant turn, TOOL spans for each tool use, and nested AGENT sub-spans for sub-agent activity. Sets trace metadata, timing, token usage, and calls flushTraces when done. Handles missing files, empty transcripts, and transcripts with no user messages gracefully by creating no spans.

Type: Function
Name: readTranscript
Location: libs/typescript/integrations/claude-code/src/transcript.ts
Signature: readTranscript(filePath: string): TranscriptEntry[]
Description: Reads a JSONL file at the given path and returns an array of TranscriptEntry objects, one per non-empty line.

Type: Function
Name: parseTimestampToNs
Location: libs/typescript/integrations/claude-code/src/transcript.ts
Signature: parseTimestampToNs(ts: string | number | null | undefined): number | null
Description: Converts a timestamp to nanoseconds. ISO date strings are parsed via Date. Numeric values are treated as nanoseconds if >= 1e13, milliseconds if >= 1e10, or Unix seconds otherwise (each returns Math.floor of the scaled value). Returns null for null, undefined, empty string, or invalid date strings.

Type: Function
Name: extractTextContent
Location: libs/typescript/integrations/claude-code/src/transcript.ts
Signature: extractTextContent(content: string | Array<{type: string; text?: string; [key: string]: any}>): string
Description: Extracts plain text from a message content value. If content is a string, returns it directly. If content is an array of content blocks, joins the text fields of all blocks with type 'text' using '\n'. Returns '' for an empty array.

Type: Function
Name: findLastUserMessageIndex
Location: libs/typescript/integrations/claude-code/src/transcript.ts
Signature: findLastUserMessageIndex(entries: TranscriptEntry[]): number | null
Description: Finds the index of the last "real" user message in the transcript. Skips tool_result messages (user entries whose message content is an array of tool_result blocks), entries marked with isCompactSummary: true, and skill injection messages (user text content starting with 'Base directory:'). Returns null for an empty transcript.

Type: Function
Name: findFinalAssistantResponse
Location: libs/typescript/integrations/claude-code/src/transcript.ts
Signature: findFinalAssistantResponse(entries: TranscriptEntry[], startIndex: number): string | null
Description: Searches from startIndex onward for the last assistant message that contains text content. Returns the extracted text of that response, or null if no such message exists.

Type: Interface/Type
Name: TranscriptEntry
Location: libs/typescript/integrations/claude-code/src/types.ts
Description: Represents a single entry in a Claude Code JSONL transcript. Must support at minimum the following fields: type (string: 'user' | 'assistant' | 'progress' | 'queue-operation'), message (optional: { role: string, content: string | ContentBlock[] }), timestamp (optional string or number), sessionId (optional string), agentId (optional string), version (optional string), permissionMode (optional string), isCompactSummary (optional boolean), toolUseResult (optional object with optional status, agentId, totalDurationMs, success, commandName fields). For 'progress' entries: parentToolUseID (string), toolUseID (string), data ({ type: string, agentId: string, prompt: string, message: TranscriptEntry }). For 'queue-operation' entries: operation (string), content (optional string).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.