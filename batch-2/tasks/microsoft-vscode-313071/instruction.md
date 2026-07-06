I'm working on the AI chat infrastructure in our editor extension and running into an issue with how we handle mode switches.

*   The createResponsesRequestBody function must accept an optional modeChanged boolean field in its request options parameter.

*   When modeChanged is true, createResponsesRequestBody must NOT set body.previous_response_id (it must remain undefined), regardless of whether a stateful marker exists in the message list.

*   When modeChanged is true, createResponsesRequestBody must strip stateful marker messages from body.input and include all non-marker messages (both before and after the removed marker) in body.input.

*   The modeChanged behavior must apply consistently to both websocket requests and non-websocket (HTTP) requests.

*   When multiple stateful markers from successive mode switches appear in the conversation history and modeChanged is true, the current matching stateful marker must be ignored and its messages stripped, while all other non-marker messages are preserved in body.input.

*   When modeChanged is false or not provided, existing stateful marker behavior must be preserved: if the current websocket marker is found in the messages, it should be used as previous_response_id and messages before it omitted from body.input.

*   DEFAULT_READ_TOOLS must be exported as a named constant from the agentTypes module in the agents vscode-node directory.

*   The Plan agent's default tools list must equal exactly the union of DEFAULT_READ_TOOLS with 'agent' and 'vscode/askQuestions', and must NOT include write tools such as 'edit', 'createFile', or 'apply_patch'.


*   Interface details: Type: Function
Name: createResponsesRequestBody
Location: extensions/copilot/src/platform/endpoint/node/responsesApi.ts
Signature: createResponsesRequestBody(servicesAccessor, options: IMakeChatRequestOptions, model, endpoint) -> { input: ResponseInputItem[]; previous_response_id?: string; ... }
Description: Builds the request body for the Responses API. The options object may include an optional boolean field modeChanged. When modeChanged is true, the function must not set previous_response_id and must exclude stateful marker messages from the input array, including all non-marker messages both before and after where the marker appeared. When modeChanged is false or absent, existing stateful marker behavior is preserved (the matching marker becomes previous_response_id and only post-marker messages are sent). Returns an object with at minimum: previous_response_id (string | undefined) and input (array of converted message objects).

Type: Interface
Name: IMakeChatRequestOptions
Location: extensions/copilot/src/platform/networking/common/networking.ts
Signature: interface IMakeChatRequestOptions { ...; modeChanged?: boolean; }
Description: The request options interface used across the chat endpoint layer. Must be extended with an optional modeChanged boolean field indicating whether the current request's mode instructions differ from the previous turn. When modeChanged is true, createResponsesRequestBody will skip stateful marker reuse.

Type: Constant
Name: DEFAULT_READ_TOOLS
Location: extensions/copilot/src/extension/agents/vscode-node/agentTypes.ts
Signature: export const DEFAULT_READ_TOOLS: string[]
Description: Named export representing the set of default read-only tool names available to the Plan agent. Must not include write tools such as 'edit', 'createFile', or 'apply_patch'. The Plan agent's default tools list is constructed as the union of DEFAULT_READ_TOOLS with 'agent' and 'vscode/askQuestions'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.