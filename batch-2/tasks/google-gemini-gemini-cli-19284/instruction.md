I'm building an experimental browser automation subagent for a CLI tool.

*   The createAnalyzeScreenshotTool function must return a tool with name 'analyze_screenshot' that accepts an 'instruction' parameter and captures a screenshot before calling the visual model.

*   When the screenshot result contains no image content item, the execute method must return an error with llmContent containing 'Failed to capture screenshot' and must NOT invoke the visual model.

*   On successful visual model response, the execute method must return llmContent containing 'Visual Analysis Result' followed by the model's text, with no error property.

*   When the visual model returns empty or missing parts in its response, the execute method must return an error with llmContent containing 'Visual model returned no analysis'.

*   When the visual model throws an error containing '404' or '403' (or 'not found' or 'permission'), the execute method must return an error with llmContent containing 'Visual analysis model is not available'.

*   When the visual model throws a generic error (not 404/403), the execute method must return an error with llmContent containing 'Visual analysis failed' followed by the error message.

*   The createBrowserAgentDefinition function must call ensureConnection() on the browser manager, call printOutput if provided, and return { definition, browserManager }. The definition must have kind='local' and include inputConfig, outputConfig, and promptConfig fields.

*   The createBrowserAgentDefinition function must return a definition whose name equals the exported BROWSER_AGENT_NAME constant.

*   When no visualModel is configured, createBrowserAgentDefinition must return a definition with 6 tools (5 discovered MCP tools + 1 type_text) and a system prompt that does NOT contain 'analyze_screenshot' or 'VISUAL IDENTIFICATION'.

*   When a visualModel is configured, createBrowserAgentDefinition must return a definition with 7 tools (including 'analyze_screenshot') and a system prompt that contains 'analyze_screenshot' and 'VISUAL IDENTIFICATION'.

*   The cleanupBrowserAgent function must call close() on the browser manager and must resolve to undefined even when close() throws an error.

*   The buildBrowserSystemPrompt function must accept a boolean parameter. When true, the returned string must contain 'VISUAL IDENTIFICATION', 'analyze_screenshot', and 'click_at'. When false, the returned string must NOT contain 'VISUAL IDENTIFICATION' or 'analyze_screenshot'.

*   Regardless of the visionEnabled argument, buildBrowserSystemPrompt must always include 'PARALLEL TOOL CALLS', 'OVERLAY/POPUP HANDLING', 'COMPLEX WEB APPS', 'TERMINAL FAILURES', and 'complete_task' in the returned string.

*   The BrowserAgentInvocation class must accept an optional toolName (4th) and toolDisplayName (5th) constructor argument. When omitted, _toolName defaults to 'browser_agent'. The constructor params must be accessible via the params property.

*   BrowserAgentInvocation.getDescription() must return a string containing 'browser agent' and the input parameter key names, truncated to at most 200 characters.

*   BrowserAgentInvocation.toolLocations() must return an empty array.

*   The BrowserManager class must use the raw MCP SDK Client (not a McpClient wrapper). It must pass { name: 'gemini-cli-browser-agent' } when constructing the Client, and must NOT call config.getMcpClientManager().

*   BrowserManager.ensureConnection() must spawn 'npx' with args including '-y', a string matching 'chrome-devtools-mcp@{version}', and '--experimental-vision'.

*   In persistent mode (default), ensureConnection() must NOT pass --isolated or --autoConnect, and must pass --userDataDir with a value whose path ends in 'cli-browser-profile'.

*   When headless is true, ensureConnection() must pass '--headless'. When profilePath is configured, it must pass '--userDataDir' followed by the configured path.

*   When sessionMode is 'isolated', ensureConnection() must pass '--isolated' and must NOT pass '--autoConnect'. When sessionMode is 'existing', it must pass '--autoConnect' and must NOT pass '--isolated'.

*   When sessionMode is 'existing' and the MCP connection fails, ensureConnection() must throw an error matching /Failed to connect to existing Chrome instance/ and also matching /chrome:\/\/inspect\/#remote-debugging/.

*   When sessionMode is persistent and the connection fails with an 'already running' error, ensureConnection() must throw errors matching /Close all Chrome windows using this profile/ and /Set sessionMode to "isolated"/.

*   When sessionMode is persistent and the connection times out, ensureConnection() must throw an error matching /Chrome is not installed/.

*   For unrecognized connection errors in persistent mode, ensureConnection() must throw an error matching /sessionMode: persistent/.

*   BrowserManager.getRawMcpClient() must return the same cached client on subsequent calls without creating a new Client instance.

*   BrowserManager.callTool() must return a result with the shape { content: McpContentItem[], isError: boolean } normalized from the raw MCP SDK response.

*   createMcpDeclarativeTools must return the discovered MCP tools PLUS one additional composite 'type_text' tool (always included). When 2 MCP tools are discovered, it returns 3 tools with names ['take_snapshot', 'click', 'type_text'].

*   Each tool returned by createMcpDeclarativeTools must have a schema with a name and a parametersJsonSchema field, and a description that contains the original MCP tool description.

*   When a tool invocation returned by createMcpDeclarativeTools executes successfully, it must return { llmContent: textContent, error: undefined }. When the MCP result has isError=true, it must return { error: { message: rawTextContent } }. When execution throws, it must return { error: { message: errorMsg } }.

*   The confirmation details returned by tool invocations from createMcpDeclarativeTools must have type: 'mcp', serverName: 'browser-agent', and the tool's name as toolName. After calling onConfirm with ProceedAlways, messageBus.publish must be called with an object containing type: MessageBusType.UPDATE_POLICY, mcpName: 'browser-agent', and persist: false.

*   The getPolicyUpdateOptions method on tool invocations from createMcpDeclarativeTools must return { mcpName: 'browser-agent' }.

*   Config.getBrowserAgentConfig() must return { enabled: boolean, model?: string, customConfig: BrowserAgentCustomConfig } with defaults: enabled=false, model=undefined, customConfig.sessionMode='persistent', customConfig.headless=false, customConfig.profilePath=undefined, customConfig.visualModel=undefined.

*   Config.getBrowserAgentConfig() must read enabled and modelConfig from agents.overrides.browser_agent, and sessionMode/headless/profilePath/visualModel from agents.browser. Partial configuration must apply defaults for unspecified fields.


*   Interface details: Type: Function
Name: createAnalyzeScreenshotTool
Location: packages/core/src/agents/browser/analyzeScreenshot.ts
Signature: createAnalyzeScreenshotTool(browserManager: BrowserManager, config: Config, messageBus: MessageBus) -> DeclarativeTool
Description: Creates a declarative tool named 'analyze_screenshot' that captures a screenshot via the browser manager and sends it to a visual model for analysis. The returned tool accepts an 'instruction' parameter. On success, returns llmContent containing 'Visual Analysis Result'. On failure cases (no image, empty model response, 404/403 errors, generic errors), returns an error with appropriate llmContent messages.

Type: Function
Name: createBrowserAgentDefinition
Location: packages/core/src/agents/browser/browserAgentFactory.ts
Signature: createBrowserAgentDefinition(config: Config, messageBus: MessageBus, printOutput?: (msg: string) => void) -> Promise<{ definition: LocalAgentDefinition, browserManager: BrowserManager }>
Description: Creates a browser agent definition with MCP tools. Calls browserManager.ensureConnection(), wraps discovered tools, and conditionally includes the analyze_screenshot tool when a visualModel is configured. The definition has kind='local', inputConfig, outputConfig, and promptConfig fields. The system prompt content depends on whether vision is enabled.

Type: Function
Name: cleanupBrowserAgent
Location: packages/core/src/agents/browser/browserAgentFactory.ts
Signature: cleanupBrowserAgent(browserManager: BrowserManager) -> Promise<void>
Description: Closes the browser manager. Handles errors gracefully — resolves to undefined even if close() throws.

Type: Function
Name: buildBrowserSystemPrompt
Location: packages/core/src/agents/browser/browserAgentDefinition.ts
Signature: buildBrowserSystemPrompt(visionEnabled: boolean) -> string
Description: Builds the system prompt for the browser agent. When visionEnabled is true, the prompt contains 'VISUAL IDENTIFICATION', 'analyze_screenshot', and 'click_at'. When false, it does NOT contain 'VISUAL IDENTIFICATION' or 'analyze_screenshot'. Regardless of visionEnabled, always contains 'PARALLEL TOOL CALLS', 'OVERLAY/POPUP HANDLING', 'COMPLEX WEB APPS', 'TERMINAL FAILURES', and 'complete_task'.

Type: Constant
Name: BROWSER_AGENT_NAME
Location: packages/core/src/agents/browser/browserAgentDefinition.ts
Description: The canonical string name for the browser agent, used as definition.name. Must match the name checked in tests against createBrowserAgentDefinition's returned definition.

Type: Class
Name: BrowserAgentInvocation
Location: packages/core/src/agents/browser/browserAgentInvocation.ts
Description: Browser agent invocation that handles async tool setup and cleanup.
Signature:
  constructor(config: Config, params: AgentInputs, messageBus: MessageBus, toolName?: string, toolDisplayName?: string)
    - Stored as _toolName (default: 'browser_agent') and _toolDisplayName
    - params are stored and accessible via .params
  getDescription() -> string
    - Returns a string containing 'browser agent' and the key names from params
    - Truncated to at most 200 characters
  toolLocations() -> []
    - Always returns an empty array

Type: Class
Name: BrowserManager
Location: packages/core/src/agents/browser/browserManager.ts
Description: Manages browser lifecycle via an isolated MCP client connected to chrome-devtools-mcp.
Signature:
  constructor(config: Config)
  getRawMcpClient() -> Promise<Client>
    - Returns cached MCP client; Client constructor called only once across multiple calls
  getDiscoveredTools() -> Promise<McpTool[]>
    - Returns tools discovered from MCP server
  callTool(toolName: string, args: Record<string, unknown>, signal?: AbortSignal) -> Promise<McpToolCallResult>
    - Returns { content: McpContentItem[], isError: boolean }
  ensureConnection() -> Promise<void>
    - Spawns 'npx' with args including '-y', 'chrome-devtools-mcp@{version}', '--experimental-vision'
    - In persistent mode (default): does NOT pass --isolated or --autoConnect; passes --userDataDir with value ending in 'cli-browser-profile'
    - Passes --headless when headless is true
    - Passes --userDataDir {profilePath} when profilePath is configured
    - Passes --isolated when sessionMode is 'isolated' (does NOT pass --autoConnect)
    - Passes --autoConnect when sessionMode is 'existing' (does NOT pass --isolated)
    - Uses raw MCP SDK Client with name 'gemini-cli-browser-agent', NOT McpClientManager
    - Does NOT call config.getMcpClientManager()
    - Throws error matching /Failed to connect to existing Chrome instance/ and /chrome:\/\/inspect\/#remote-debugging/ for existing-mode connection failures
    - Throws error matching /Close all Chrome windows using this profile/ and /Set sessionMode to "isolated"/ when persistent mode hits "already running" error
    - Throws error matching /Chrome is not installed/ for persistent mode timeout
    - Throws error matching /sessionMode: persistent/ in the generic fallback error
  close() -> Promise<void>
    - Closes the MCP client connection

Type: Interface
Name: McpToolCallResult
Location: packages/core/src/agents/browser/browserManager.ts
Description: Result from an MCP tool call. Fields: content?: McpContentItem[], isError?: boolean

Type: Interface
Name: McpContentItem
Location: packages/core/src/agents/browser/browserManager.ts
Description: Content item from MCP tool call. Fields: type: 'text' | 'image', text?: string, data?: string, mimeType?: string

Type: Function
Name: createMcpDeclarativeTools
Location: packages/core/src/agents/browser/mcpToolWrapper.ts
Signature: createMcpDeclarativeTools(browserManager: BrowserManager, messageBus: MessageBus) -> Promise<DeclarativeTool[]>
Description: Creates declarative tools from discovered MCP tools PLUS a composite 'type_text' tool (always included). When 2 MCP tools are discovered, returns 3 tools. Each tool has .name, .description (contains the original MCP description), .schema.name, and .schema.parametersJsonSchema. The built invocation's execute() calls browserManager.callTool(toolName, params, signal). On success: { llmContent: textContent, error: undefined }. On MCP error (isError=true): { error: { message: rawTextContent }, llmContent: 'Error: ...' }. On thrown exception: { error: { message: errorMsg } }. Confirmation details have type: 'mcp', serverName: 'browser-agent', toolName. After onConfirm with ProceedAlways, publishes to messageBus with type: MessageBusType.UPDATE_POLICY, mcpName: 'browser-agent', persist: false. getPolicyUpdateOptions() returns { mcpName: 'browser-agent' }.

Type: Method
Name: getBrowserAgentConfig
Location: packages/core/src/config/config.ts
Signature: getBrowserAgentConfig() -> { enabled: boolean, model?: string, customConfig: BrowserAgentCustomConfig }
Description: Returns browser agent configuration from agents.overrides.browser_agent and agents.browser. Defaults: enabled=false, model=undefined, customConfig.sessionMode='persistent', customConfig.headless=false, customConfig.profilePath=undefined, customConfig.visualModel=undefined. When agents.overrides.browser_agent.modelConfig.model is set, returns it as model. When agents.browser fields are set, they override defaults. Partial config applies defaults to unspecified fields (e.g., omitting sessionMode still yields 'persistent').

Type: Interface
Name: BrowserAgentCustomConfig
Location: packages/core/src/config/config.ts
Description: Browser agent custom configuration. Fields: sessionMode?: 'isolated' | 'persistent' | 'existing', headless?: boolean, profilePath?: string, visualModel?: string


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.