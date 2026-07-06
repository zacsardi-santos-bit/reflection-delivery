I'm working with the MCP provider in promptfoo and running into a few issues.

*   normalizeResponseTransformResult must accept any value and return a ProviderResponse: if the value is a non-null, non-array object that contains an 'output' or 'error' property, return it unchanged; otherwise, wrap it as { output: value }. Located at src/providers/transformResult.ts.

*   parseFileTransformReference must parse a file:// reference string and return { filename, functionName? }: strip the 'file://' prefix, then split on the last colon to extract an optional named export (functionName). For POSIX paths like 'file://path/to/parser.js:parse', return { filename: 'path/to/parser.js', functionName: 'parse' }. For Windows paths like 'file://C:\path\parser.js' with no named export, return { filename: 'C:\path\parser.js' }. For Windows paths like 'file://C:\path\parser.js:parse', split on the LAST colon only, returning { filename: 'C:\path\parser.js', functionName: 'parse' }. Located at src/providers/transformUtils.ts.

*   MCPClient.callTool() must return an object containing both a 'content' field (the normalized string representation) and a 'raw' field (the original, unmodified response object from the MCP SDK). For example, when the tool returns { content: 'result' }, callTool returns { content: 'result', raw: { content: 'result' } }. When tool response content is falsy, raw should be the original response object (e.g., {}). Located at src/providers/mcp/client.ts.

*   MCPClient.initialize() must resolve local server paths relative to cliState.basePath when it is set. When cliState.basePath is defined and a server is configured with a relative path (e.g., './example-server.js'), the resolved absolute path must be computed as path.resolve(cliState.basePath, serverPath) and used when spawning the stdio server process. Located at src/providers/mcp/client.ts.

*   createTransformResponse (in src/providers/mcp/transforms.ts) must accept an optional transform (string, function, or undefined) and return an async function with signature (result: unknown, content: string, context: MCPTransformResponseContext) => Promise<ProviderResponse>. When transform is undefined, return a function that returns { output: content }. When transform is a function, call it with (result, content, context), await the result, and normalize via normalizeResponseTransformResult. When transform is an async function, await the returned promise before normalizing.

*   createTransformResponse (in src/providers/mcp/transforms.ts) must handle string transforms: when the string is a JavaScript expression (e.g., '42' or '({ answer: result.x })'), evaluate it with variables 'result', 'content', and 'context' in scope, await the result, and normalize via normalizeResponseTransformResult. When the string is a function expression (arrow or regular function), invoke it with (result, content, context) and normalize the result. Wrap execution errors in a new error whose message matches /Failed to transform MCP response/.

*   createTransformResponse (in src/providers/mcp/transforms.ts) must throw synchronously with an error message matching /should be pre-loaded before calling createTransformResponse/ when passed a string starting with 'file://'. When passed a value of an unsupported type (e.g., number), it must throw with the exact message: "Unsupported response transform type: number. Expected a function, a string starting with 'file://' pointing to a JavaScript file, or a string containing a JavaScript expression."

*   MCPTransformResponseContext must be an interface exported from src/providers/mcp/transforms.ts with fields: toolName (string), toolArgs (Record<string, unknown>), and originalPayload (optional unknown).

*   MCPProvider.callApi() must, when the prompt variable contains valid JSON, extract toolName and args, call the tool, apply the configured response transform (if any), and return { output, raw, metadata: { toolName, toolArgs, originalPayload } }. The raw field must be the original MCP SDK response (from the callTool result's raw property). Provider-controlled metadata fields (toolName, toolArgs, originalPayload) must always override any conflicting values returned by the transform's metadata. Located at src/providers/mcp/index.ts.

*   MCPProvider.callApi() must return { error: 'Invalid JSON in prompt. MCP provider expects a JSON payload with tool call information.' } when the prompt variable does not contain valid JSON, and must not invoke the MCP tool in this case.

*   MCPProvider.callTool() must apply the configured response transform (if any) and return { output, raw, metadata: { toolName, toolArgs } }. Unlike callApi(), the metadata for direct tool calls must NOT include originalPayload. Located at src/providers/mcp/index.ts.

*   createTransformResponse in src/providers/httpTransforms.ts must evaluate a numeric string expression (e.g., '42') by returning the numeric value wrapped as { output: 42 } rather than a string. This is achieved by normalizeResponseTransformResult, which wraps any non-ProviderResponse value as { output: value }.


*   Interface details: Type: Function
Name: normalizeResponseTransformResult
Location: src/providers/transformResult.ts
Signature: normalizeResponseTransformResult(value: unknown): ProviderResponse
Description: Checks whether the given value is already a ProviderResponse (a non-null, non-array object with an 'output' or 'error' property). If so, returns it unchanged. Otherwise, wraps it as { output: value }.

---

Type: Function
Name: parseFileTransformReference
Location: src/providers/transformUtils.ts
Signature: parseFileTransformReference(reference: string): { filename: string; functionName?: string }
Description: Parses a file:// reference string by stripping the 'file://' prefix and splitting on the last colon to separate the filename from an optional named export (functionName). Correctly handles Windows paths (e.g., C:\path\parser.js) by only splitting on a colon that is followed by a valid function name and preceded by a filename recognized as a JavaScript file, ensuring the Windows drive letter colon is not mistaken for a named-export separator.

---

Type: Function
Name: createTransformResponse
Location: src/providers/mcp/transforms.ts
Signature: createTransformResponse(parser: string | Function | undefined): (result: unknown, content: string, context: MCPTransformResponseContext) => Promise<ProviderResponse>
Description: Factory that creates a response transform for MCP tool results. When parser is undefined, returns a transform that produces { output: content }. When parser is a function (sync or async), calls it with (result, content, context) and normalizes the return value via normalizeResponseTransformResult. When parser is a JavaScript expression string, evaluates it with result, content, and context in scope and normalizes the result; wraps evaluation errors in a new error whose message starts with "Failed to transform MCP response". When parser is a 'file://' string, throws synchronously (file references must be pre-loaded before calling this function). When parser is any other type, throws: "Unsupported response transform type: <typeof parser>. Expected a function, a string starting with 'file://' pointing to a JavaScript file, or a string containing a JavaScript expression."

---

Type: Interface
Name: MCPTransformResponseContext
Location: src/providers/mcp/transforms.ts
Description: Context passed to MCP response transforms describing the tool call.
Signature:
  toolName: string
  toolArgs: Record<string, unknown>
  originalPayload?: unknown

---

Type: Class method modification
Name: callTool
Location: src/providers/mcp/client.ts (MCPClient class)
Signature: callTool(toolName: string, args: Record<string, unknown>): Promise<{ content: string; raw: unknown }>
Description: In addition to the existing 'content' field (normalized string), callTool now also returns a 'raw' field containing the original, unmodified response object from the MCP SDK call. The raw field is set to the result object before content normalization.

---

Type: Class method modification
Name: initialize
Location: src/providers/mcp/client.ts (MCPClient class)
Description: When cliState.basePath is set and a local server path is configured, the server path is resolved to an absolute path using path.resolve(cliState.basePath, server.path) before being passed to StdioClientTransport.

---

Type: Class modification
Name: MCPProvider
Location: src/providers/mcp/index.ts
Description: MCPProvider now accepts an optional 'transformResponse' (or deprecated 'responseParser') field in its config. This is loaded via loadTransformModule and passed to the MCP-specific createTransformResponse factory. callApi() applies the transform and returns { output, raw, metadata: { toolName, toolArgs, originalPayload } } where provider metadata is authoritative over transform metadata. callTool() applies the transform and returns { output, raw, metadata: { toolName, toolArgs } } (no originalPayload). For invalid JSON in the prompt variable, callApi() returns { error: 'Invalid JSON in prompt. MCP provider expects a JSON payload with tool call information.' } without calling the tool.

---

Type: Function
Name: loadTransformModule
Location: src/providers/transformUtils.ts
Signature: loadTransformModule(transform: string | Function | undefined): Promise<string | Function | undefined>
Description: Pre-loads a transform from a file:// reference by resolving the filename relative to cliState.basePath and importing the module. If the resolved module is a function, returns it. If it is not a function, throws. For non-file transforms (functions or plain strings), returns the input unchanged.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.