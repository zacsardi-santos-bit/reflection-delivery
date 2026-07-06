I'm working on adding MLflow tracing support for a coding assistant.

*   readTranscript must read a JSONL file at the given path and return one parsed RolloutLine object per line.

*   parseTimestampToNs must parse a valid ISO 8601 timestamp string into a positive number representing nanoseconds. It must return null for null, undefined, or empty string inputs.

*   extractTextFromContent must join all text fields in a content-block array with '\n' when given an array; return the string unchanged when given a plain string; return '' when given undefined.

*   findLastUserPrompt must return an object with a `text` property containing the last user message text found in the transcript, or null if no user message is present.

*   getLastTurnRecords must return only the records belonging to the most recent turn, starting from (and including) the task_started event_msg and ending at (and including) the task_complete event_msg. The first record in the result must have type 'event_msg'.

*   getTokenUsage must locate the token_count event in the transcript and return an object with input_tokens, output_tokens, and total_tokens fields as numbers. Returns null if no token count event is found.

*   getModel must return the model name from the session_meta record, or the string 'unknown' if no model is recorded in the session metadata.

*   getSessionId must return the session ID string stored in the session_meta record.

*   buildToolResultMap must return a Record<string, string> mapping each call_id to its corresponding tool output string by scanning function_call_output records. Must return an empty object when no function_call_output records exist.

*   processNotify must create an AGENT-type root span named 'codex_conversation'. The root span inputs must be the last string in the 'input-messages' array (raw string, not wrapped). The root span outputs must be the 'last-assistant-message' value (raw string). When 'input-messages' is empty, processNotify must create no spans and must not call flushTraces.

*   processNotify must create one LLM-type child span (parented to the root span) named 'llm_call'. Its inputs must be { model: 'unknown', messages: [{ role: 'user', content: <last input message> }] } and its outputs must be { choices: [{ message: { role: 'assistant', content: <last-assistant-message> } }] }.

*   processNotify must set the trace metadata key 'mlflow.trace.session' to the value of 'thread-id' from the payload, and must set 'mlflow.trace.user' to a defined (non-null, non-undefined) value. processNotify must call flushTraces after span creation.

*   reconstructMessages must convert response_item records (up to but not including uptoIndex) into OpenAI chat-format messages. User messages map to { role: 'user', content: string }; assistant messages to { role: 'assistant', content: string }; developer messages to { role: 'system', content: string }; function_call records to { role: 'assistant', content: null, tool_calls: [{ id: call_id, type: 'function', function: { name, arguments } }] }; function_call_output records to { role: 'tool', tool_call_id: call_id, content: output }. Messages whose extracted text is empty or whitespace-only must be skipped.

*   createChildSpans must create one LLM span named 'llm_call' per assistant message and one TOOL span named 'tool_<function_name>' per function_call in the turn, all parented to the given parent span. LLM span inputs are { model, messages } where messages is the reconstructed conversation history up to (not including) that assistant message. LLM span outputs are { choices: [{ message: { role: 'assistant', content } }] }. TOOL span inputs are the parsed JSON arguments object; TOOL span attributes must include tool_name (function name) and tool_id (call_id); TOOL span outputs are { result: string }.

*   createChildSpans must assign accurate timestamps to each span. The first LLM span's startTimeNs is the task_started timestamp; subsequent LLM spans' startTimeNs is the timestamp of the preceding function_call_output record. Each LLM span's endTimeNs is the timestamp of its corresponding assistant message. Each TOOL span's startTimeNs is the function_call timestamp and endTimeNs is the timestamp of the matching function_call_output (matched by call_id). All child span timestamps must fall within [findTaskStartedNs, findTaskCompleteNs].

*   buildToolStatuses must scan exec_command_end event_msg records and return a map from call_id to { failed: boolean, exitCode: number }. A tool is marked failed (failed: true) when its exec_command_end has status 'failed' or a non-zero exit_code.

*   createChildSpans must call setStatus with the ERROR status code and a message containing the exit code on TOOL spans whose exec_command_end reports failure. For tool calls that succeed (exit_code 0), setStatus must NOT be called on the TOOL span.

*   findTaskStartedNs must return the nanosecond timestamp of the task_started event_msg, or null if none exists in the turn. findTaskCompleteNs must return the nanosecond timestamp of the task_complete event_msg, or null if none exists in the turn.


*   Interface details: Type: Function
Name: readTranscript
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: readTranscript(filePath: string): RolloutLine[]
Description: Reads a JSONL transcript file from disk and returns an array of parsed RolloutLine records (one per line).

Type: Function
Name: parseTimestampToNs
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: parseTimestampToNs(input: string | null | undefined): number | null
Description: Parses an ISO 8601 timestamp string into a nanosecond integer. Returns null for null, undefined, or empty string inputs.

Type: Function
Name: extractTextFromContent
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: extractTextFromContent(content: { type: string; text: string }[] | string | undefined): string
Description: Extracts plain text from a content block array (joining entries with '\n'), returns a string argument unchanged, and returns '' for undefined.

Type: Function
Name: findLastUserPrompt
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: findLastUserPrompt(records: RolloutLine[]): { text: string } | null
Description: Returns an object with a `text` property containing the last user message text found in the transcript, or null if none exists.

Type: Function
Name: getLastTurnRecords
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: getLastTurnRecords(records: RolloutLine[]): RolloutLine[]
Description: Returns the subset of records belonging to the most recent turn — from the task_started event_msg through the task_complete event_msg, inclusive.

Type: Function
Name: getTokenUsage
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: getTokenUsage(records: RolloutLine[]): { input_tokens: number; output_tokens: number; total_tokens: number } | null
Description: Finds the token_count event in the transcript and returns an object with input_tokens, output_tokens, and total_tokens. Returns null if no token count event is found.

Type: Function
Name: getModel
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: getModel(records: RolloutLine[]): string
Description: Returns the model name from the session_meta record. Returns the string 'unknown' when no model is present in the session metadata.

Type: Function
Name: getSessionId
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: getSessionId(records: RolloutLine[]): string
Description: Returns the session ID from the session_meta record in the transcript.

Type: Function
Name: buildToolResultMap
Location: libs/typescript/integrations/codex/src/transcript.ts
Signature: buildToolResultMap(records: RolloutLine[]): Record<string, string>
Description: Builds a map from call_id strings to tool output strings by scanning function_call_output records. Returns an empty object if no tool outputs are present.

Type: Function
Name: processNotify
Location: libs/typescript/integrations/codex/src/tracing.ts
Signature: processNotify(payload: NotifyPayload): Promise<void>
Description: Processes an agent-turn-complete notification payload, creating MLflow spans for the completed turn. Creates an AGENT root span named 'codex_conversation' with the last input message as its raw string input and the last-assistant-message as its raw string output. Also creates an LLM child span named 'llm_call'. Sets trace metadata keys 'mlflow.trace.session' (to thread-id) and 'mlflow.trace.user'. Skips all processing and does not flush if input-messages is empty. Calls flushTraces after span creation.

Type: Function
Name: reconstructMessages
Location: libs/typescript/integrations/codex/src/tracing.ts
Signature: reconstructMessages(items: RolloutLine[], uptoIndex: number): Array<ChatMessage>
Description: Converts transcript response_item records (up to but not including uptoIndex) into OpenAI chat-format messages. Maps user/assistant message records to { role, content } objects; developer messages to { role: 'system', content }; function_call records to { role: 'assistant', content: null, tool_calls: [{ id, type: 'function', function: { name, arguments } }] }; function_call_output records to { role: 'tool', tool_call_id, content }. Skips messages whose text is empty or whitespace-only.

Type: Function
Name: createChildSpans
Location: libs/typescript/integrations/codex/src/tracing.ts
Signature: createChildSpans(parent: { spanId: string }, turn: RolloutLine[], model: string): void
Description: Creates MLflow LLM and TOOL child spans from a transcript turn. One LLM span (named 'llm_call') per assistant message, one TOOL span (named 'tool_<function_name>') per function_call. All spans are parented to parent.spanId. LLM span inputs are { model, messages: ChatMessage[] } using reconstructed conversation history; LLM span outputs are { choices: [{ message: { role: 'assistant', content } }] }. TOOL span inputs are the parsed JSON arguments; TOOL span attributes include tool_name and tool_id (the call_id); TOOL span outputs are { result: string }. Timestamps are derived from turn record timestamps: first LLM span starts at task_started, subsequent LLM spans start at the previous function_call_output timestamp; TOOL spans run from function_call to its matching function_call_output. If exec_command_end indicates failure (status='failed' or exit_code != 0), calls setStatus with ERROR code and a message containing the exit code; does NOT call setStatus for successful tool calls.

Type: Function
Name: findTaskStartedNs
Location: libs/typescript/integrations/codex/src/tracing.ts
Signature: findTaskStartedNs(turn: RolloutLine[]): number | null
Description: Returns the nanosecond timestamp of the task_started event_msg in the turn, or null if no such event exists.

Type: Function
Name: findTaskCompleteNs
Location: libs/typescript/integrations/codex/src/tracing.ts
Signature: findTaskCompleteNs(turn: RolloutLine[]): number | null
Description: Returns the nanosecond timestamp of the task_complete event_msg in the turn, or null if no such event exists.

Type: Function
Name: buildToolStatuses
Location: libs/typescript/integrations/codex/src/tracing.ts
Signature: buildToolStatuses(turn: RolloutLine[]): Record<string, { failed: boolean; exitCode: number }>
Description: Scans exec_command_end event_msg records in the turn and returns a map from call_id to { failed: boolean, exitCode: number }. A tool is marked failed when its status is 'failed' or its exit_code is non-zero.

Type: Interface
Name: NotifyPayload
Location: libs/typescript/integrations/codex/src/types.ts
Description: Payload type for agent-turn-complete notifications.
Signature:
  type: string;
  'thread-id': string;
  'turn-id': string;
  cwd: string;
  client: string;
  'input-messages': string[];
  'last-assistant-message': string;

Type: Interface
Name: RolloutLine
Location: libs/typescript/integrations/codex/src/types.ts
Description: Represents a single line of a JSONL transcript file.
Signature:
  timestamp: string;
  type: 'session_meta' | 'event_msg' | 'response_item';
  payload: record with at minimum a `type` string field, plus optional fields depending on record type (role, content, name, call_id, arguments, output, exit_code, status, info, id, cwd, originator, cli_version, source);


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.