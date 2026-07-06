I'm working on an adapter that wraps a third-party AI streaming SDK to make it compatible with the OpenAI streaming format.

*   The convertToolsToVercelFormat function must return tool objects where the parameter schema is stored under the property name 'inputSchema' (not 'parameters'). The 'inputSchema' value must be defined and truthy for every converted tool.

*   The VercelStreamPart union type must define 'text-delta' parts with 'id' and 'text' fields (not 'textDelta'). The convertVercelStreamPart function must read content from the 'text' field when handling 'text-delta' parts.

*   The VercelStreamPart union type must use the type name 'reasoning-delta' (not 'reasoning') for reasoning content parts. These parts have 'id' and 'text' fields (not 'textDelta'). The convertVercelStreamPart function must handle 'reasoning-delta' and produce a chat chunk with reasoning delta content.

*   The VercelStreamPart union type must define 'tool-call' parts with an 'input' field (not 'args') for the tool call arguments. The convertVercelStreamPart function must read arguments from 'input' when handling 'tool-call' parts.

*   The VercelStreamPart union type must use the type name 'tool-input-delta' (not 'tool-call-delta') for streaming tool input deltas. These parts have 'id' and 'delta' fields (not 'toolCallId', 'toolName', 'argsTextDelta'). The convertVercelStreamPart function must handle 'tool-input-delta' and produce a tool call delta chunk.

*   The VercelStreamPart union type must define 'finish' parts with a 'totalUsage' field (not 'usage') containing 'inputTokens' and 'outputTokens' (not 'promptTokens' and 'completionTokens'). The convertVercelStreamPart function must read token counts from 'totalUsage.inputTokens' and 'totalUsage.outputTokens'.

*   The VercelStreamPart union type must use the type name 'start-step' (not 'step-start') for step lifecycle start events. The 'start-step' part requires only the 'type' field. The convertVercelStreamPart function must return null for 'start-step' parts.

*   The VercelStreamPart union type must use the type name 'finish-step' (not 'step-finish') for step lifecycle finish events. The 'finish-step' part has 'response', 'finishReason', and 'usage' fields where usage uses 'inputTokens' and 'outputTokens' (not 'promptTokens' and 'completionTokens'). The convertVercelStreamPart function must return null for 'finish-step' parts.

*   The VercelStreamPart union type must define 'file' parts with a nested 'file' object (containing 'name' and 'content') rather than flat 'name' and 'content' fields at the top level. The convertVercelStreamPart function must return null for 'file' parts.

*   The VercelStreamPart union type must use the type name 'tool-input-start' (not 'tool-call-streaming-start') for the start of a streaming tool input. These parts have an 'id' field (not 'toolCallId') and a 'toolName' field. The convertVercelStreamPart function must return null for 'tool-input-start' parts.

*   The VercelStreamPart union type must NOT include 'reasoning-signature' or 'redacted-reasoning' as valid part types.

*   The convertVercelStream function must correctly process a stream containing the updated part shapes ('start-step', 'text-delta' with 'text' field, 'tool-call' with 'input' field, 'finish-step' with renamed usage fields, 'finish' with 'totalUsage') and produce the correct number and content of OpenAI-compatible chunks.


*   Interface details: Type: Function
Name: convertToolsToVercelFormat
Location: packages/openai-adapters/src/convertToolsToVercel.ts
Signature: convertToolsToVercelFormat(tools: ChatCompletionCreateParams["tools"] | undefined) -> Promise<Record<string, { description?: string; inputSchema: unknown }> | undefined>
Description: Converts OpenAI-format tool definitions to the Vercel AI SDK format. Each resulting tool object must have an `inputSchema` property (not `parameters`) containing the wrapped parameter schema.

Type: Function
Name: convertVercelStreamPart
Location: packages/openai-adapters/src/vercelStreamConverter.ts
Signature: convertVercelStreamPart(part: VercelStreamPart, options: object) -> ChatCompletionChunk | null
Description: Converts a single Vercel AI SDK stream part into an OpenAI-compatible ChatCompletionChunk, or returns null for parts with no OpenAI equivalent.

Type: Function
Name: convertVercelStream
Location: packages/openai-adapters/src/vercelStreamConverter.ts
Signature: convertVercelStream(stream: AsyncIterable<VercelStreamPart>, options: object) -> AsyncGenerator<ChatCompletionChunk>
Description: Converts a Vercel AI SDK stream of parts into an async generator of OpenAI-compatible ChatCompletionChunks, filtering out null parts and propagating errors.

Type: TypeAlias
Name: VercelStreamPart
Location: packages/openai-adapters/src/vercelStreamConverter.ts
Description: Discriminated union type for all Vercel AI SDK stream event objects. The following member shapes are required by the tests:

- `{ type: "text-delta"; id: string; text: string }` — text content delta (field was previously `textDelta`)
- `{ type: "reasoning-delta"; id: string; text: string }` — reasoning content delta (type was previously `"reasoning"`, field was previously `textDelta`)
- `{ type: "tool-call"; toolCallId: string; toolName: string; input: unknown }` — complete tool call (field was previously `args`)
- `{ type: "tool-input-delta"; id: string; delta: string }` — streaming tool input delta (type was previously `"tool-call-delta"`, fields were previously `toolCallId`, `toolName`, `argsTextDelta`)
- `{ type: "finish"; finishReason: string; totalUsage: { inputTokens: number; outputTokens: number; totalTokens: number } }` — stream finish with usage (field was previously `usage` with `promptTokens`/`completionTokens`)
- `{ type: "start-step" }` — step lifecycle start (type was previously `"step-start"`, no extra fields)
- `{ type: "finish-step"; response: object; usage: { inputTokens: number; outputTokens: number; totalTokens: number }; finishReason: string }` — step lifecycle finish (type was previously `"step-finish"`, usage fields renamed)
- `{ type: "file"; file: { name: string; content: string } }` — file part with nested object (was previously flat `name`/`content`)
- `{ type: "tool-input-start"; id: string; toolName: string }` — start of streaming tool input (type was previously `"tool-call-streaming-start"`, field `id` was previously `toolCallId`)
- `{ type: "error"; error: Error }` — error event
- `{ type: "source"; source: unknown }` — source event (returns null)
- `{ type: "tool-result"; toolCallId: string; result: unknown }` — tool result (returns null)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.