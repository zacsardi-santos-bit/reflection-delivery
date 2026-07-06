I'm building an integration that records coding agent sessions as JSONL transcripts and I want to feed those recordings into MLflow's tracing system so I can visualize and analyze the sessions.

*   The module at libs/typescript/integrations/qwen-code/src/types.ts must export a ChatRecord type with required fields uuid, parentUuid, sessionId, timestamp, and type ('user' | 'assistant' | 'system' | 'tool_result'), and optional fields message (structured object with role and parts array, or plain string), toolCallResult (with callId, status, optional resultDisplay), usageMetadata (with promptTokenCount, candidatesTokenCount, totalTokenCount), and model.

*   The module at libs/typescript/integrations/qwen-code/src/types.ts must export a GeminiPart type covering text parts ({ text: string; thought?: boolean }), functionCall parts ({ functionCall: { id, name, args? } }), and functionResponse parts ({ functionResponse: { id, name, response } }).

*   parseTimestampToNs must convert an ISO 8601 timestamp string to a positive number representing nanoseconds (epoch ms * 1,000,000). It must return null for null, undefined, or empty string inputs.

*   getMessageText must join all non-thought text parts from a ChatRecord's message with a newline separator. It must exclude parts where thought=true by default, include them when the second argument is true, accept a plain string message (returning it as-is), and silently skip functionCall and functionResponse parts.

*   getFunctionCalls must extract and return all functionCall parts from a ChatRecord's message as an array of objects with id, name, and args fields. It must return an empty array for records that have no message or no functionCall parts.

*   isTextPart must return true for objects that have a text property and false otherwise. isFunctionCallPart must return true for objects that have a functionCall property and false otherwise.

*   buildToolResultMap must return a Map<string, ChatRecord> where keys are toolCallResult.callId values, containing only tool_result records from the input array.

*   formatResultDisplay must return the input unchanged when it is a string, return JSON.stringify(input) when it is an object, and return an empty string when the input is null or undefined.

*   getToolOutput must return the stringified resultDisplay from toolCallResult when present. When resultDisplay is absent, it must fall back to JSON.stringify of the functionResponse.response found in message parts. When neither is available, it must return an empty string.

*   readTranscript must read the JSONL file at the given path, parse each line as a JSON object, and return an array of ChatRecord objects.

*   getLastTurnRecords must return a slice of the records array starting at the last record whose type is 'user' and continuing to the end.

*   getTokenUsage must map a usageMetadata object's promptTokenCount to the 'input' field, candidatesTokenCount to 'output', and totalTokenCount to 'total'. It must return null when the argument is undefined or missing.

*   processTranscript must accept a nullable path and an optional sessionId string. When called with null, it must return immediately without creating any spans and without calling flushTraces. For a valid path, it must create an AGENT-type root span named 'qwen_code_conversation', one LLM-type span named 'llm_call' for each assistant record, and one TOOL-type span named 'tool_<functionName>' for each functionCall part within an assistant record.

*   processTranscript must set the root span's inputs to the raw user prompt text (string, not wrapped in an object) and the root span's outputs to the final assistant text (raw string).

*   processTranscript must set LLM span outputs to OpenAI choices format: { choices: [{ message: { role: 'assistant', content: <text without thought parts> } }] }. LLM span inputs must be in OpenAI chat-completion request format including a messages array and a model field. Messages in the input must exclude thought parts from assistant text, represent functionCall parts as tool_calls entries ({ id, type: 'function', function: { name, arguments: JSON.stringify(args) } }), and represent tool results as { role: 'tool', tool_call_id, content } entries.

*   processTranscript must set each TOOL span's attributes.tool_id to the functionCall id, its inputs to the functionCall args, and its end outputs to { result: <string> } where the string is the formatted resultDisplay. Successful TOOL spans must not have setStatus called. Cancelled TOOL spans must have setStatus called with ERROR status code ('STATUS_CODE_ERROR') and a message containing the word 'cancelled'.

*   processTranscript must set session metadata: the key 'mlflow.trace.session' must be set to the provided sessionId, and the key 'mlflow.trace.user' must be set to a defined value.

*   processTranscript must set the 'mlflow.chat.tokenUsage' attribute with input_tokens, output_tokens, and total_tokens fields. On the root span, input_tokens must be the promptTokenCount from the LAST assistant record (not a sum), and output_tokens must be the sum of candidatesTokenCount across all assistant records. On each individual LLM span, both values must reflect that specific API call's metadata.

*   processTranscript must record span timing in nanoseconds. The root span's startTimeNs must be the timestamp of the first user record; its endTimeNs must be the timestamp of the last assistant record. Each LLM span's startTimeNs must be the timestamp of the preceding record; its endTimeNs must be its own assistant record timestamp. Each TOOL span's startTimeNs must be the timestamp of the assistant record that emitted the function call; its endTimeNs must be the timestamp of the matching tool_result record.

*   processTranscript must call flushTraces after processing a valid (non-null) transcript path.

*   reconstructMessages must accept an array of ChatRecord objects and a target index (integer), and return an array of OpenAI-format message objects for all records before the target index. User records become { role: 'user', content: <text> }. Assistant records with only text parts become { role: 'assistant', content: <text excluding thought parts> }. Assistant records containing functionCall parts become { role: 'assistant', content: null, tool_calls: [{id, type: 'function', function: {name, arguments: JSON.stringify(args)}}] }. tool_result records become { role: 'tool', tool_call_id: <callId>, content: <stringified resultDisplay> }; when resultDisplay is absent, the content must be JSON.stringify of functionResponse.response. System records with a message must become { role: 'system', content: <text> }. System records without a message must be excluded.


*   Interface details: Type: Function
Name: parseTimestampToNs
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: parseTimestampToNs(timestamp: string | null | undefined): number | null
Description: Converts an ISO 8601 timestamp string to nanoseconds (epoch milliseconds * 1,000,000). Returns null for null, undefined, or empty string input.

Type: Function
Name: getMessageText
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: getMessageText(record: ChatRecord, includeThoughts?: boolean): string
Description: Extracts text content from a ChatRecord's message. Joins multiple text parts with newline. Excludes parts where thought=true by default; includes them when includeThoughts=true. Accepts plain string messages. Skips functionCall and functionResponse parts (they contribute no text).

Type: Function
Name: getFunctionCalls
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: getFunctionCalls(record: ChatRecord): Array<{ id: string; name: string; args?: Record<string, any> }>
Description: Extracts all functionCall parts from a ChatRecord's message. Returns an empty array for records with no message or no functionCall parts.

Type: Function
Name: isTextPart
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: isTextPart(part: GeminiPart): boolean
Description: Type guard that returns true when a GeminiPart has a text property.

Type: Function
Name: isFunctionCallPart
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: isFunctionCallPart(part: GeminiPart): boolean
Description: Type guard that returns true when a GeminiPart has a functionCall property.

Type: Function
Name: buildToolResultMap
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: buildToolResultMap(records: ChatRecord[]): Map<string, ChatRecord>
Description: Builds a Map indexed by toolCallResult.callId from tool_result records in the array. Non-tool_result records are excluded.

Type: Function
Name: formatResultDisplay
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: formatResultDisplay(display: any): string
Description: Normalizes a resultDisplay value to string. Passes through string values unchanged. JSON.stringifies object values. Returns empty string for null or undefined.

Type: Function
Name: getToolOutput
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: getToolOutput(record: ChatRecord): string
Description: Extracts tool output from a tool_result record. Prefers toolCallResult.resultDisplay (passed through formatResultDisplay). Falls back to JSON.stringify of functionResponse.response from message parts when resultDisplay is absent. Returns empty string if neither is available.

Type: Function
Name: readTranscript
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: readTranscript(path: string): ChatRecord[]
Description: Reads a JSONL file at the given path, parses each line as a JSON object, and returns the array of ChatRecord objects.

Type: Function
Name: getLastTurnRecords
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: getLastTurnRecords(records: ChatRecord[]): ChatRecord[]
Description: Returns a slice of the records array starting at the last record with type === 'user' through the end of the array.

Type: Function
Name: getTokenUsage
Location: libs/typescript/integrations/qwen-code/src/transcript.ts
Signature: getTokenUsage(usageMetadata: any): { input: number; output: number; total: number } | null
Description: Extracts token counts from a usageMetadata object. Maps promptTokenCount to input, candidatesTokenCount to output, totalTokenCount to total. Returns null for undefined/missing input.

Type: Function
Name: processTranscript
Location: libs/typescript/integrations/qwen-code/src/tracing.ts
Signature: processTranscript(transcriptPath: string | null, sessionId?: string): Promise<void>
Description: Reads a JSONL conversation transcript and converts it into MLflow spans. When transcriptPath is null, returns immediately without creating any spans or calling flushTraces. For a valid path, creates an AGENT root span named 'qwen_code_conversation', one LLM span named 'llm_call' per assistant record, and TOOL spans per functionCall part. Sets timing, token usage, session metadata, and span inputs/outputs. Calls flushTraces at the end.

Type: Function
Name: reconstructMessages
Location: libs/typescript/integrations/qwen-code/src/tracing.ts
Signature: reconstructMessages(records: ChatRecord[], targetIndex: number): OpenAIMessage[]
Description: Converts a slice of ChatRecord objects (from the beginning up to but not including targetIndex) into OpenAI-format chat messages. User records become role:'user', assistant records become role:'assistant' (with tool_calls when containing functionCall parts, content:null in that case), tool_result records become role:'tool', system records with a message become role:'system', and system records without a message are skipped.

Type: Interface/Type
Name: ChatRecord
Location: libs/typescript/integrations/qwen-code/src/types.ts
Description: Represents a single record in a conversation transcript JSONL file. Required fields: uuid (string), parentUuid (string | null), sessionId (string), timestamp (string), type ('user' | 'assistant' | 'system' | 'tool_result'). Optional fields: message (object with role and parts array, or plain string), toolCallResult (object with callId, status, optional resultDisplay), usageMetadata (object with promptTokenCount, candidatesTokenCount, totalTokenCount), model (string).

Type: Interface/Type
Name: GeminiPart
Location: libs/typescript/integrations/qwen-code/src/types.ts
Description: Union type for message parts. Text part: { text: string; thought?: boolean }. FunctionCall part: { functionCall: { id: string; name: string; args?: Record<string, any> } }. FunctionResponse part: { functionResponse: { id: string; name: string; response: any } }.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.