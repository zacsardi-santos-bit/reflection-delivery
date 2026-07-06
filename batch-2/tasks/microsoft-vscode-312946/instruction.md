I'm working on simplifying the API for the Claude Code session management layer in our VS Code extension.

*   The ClaudeLanguageModelServer class must expose a getConfig() method that returns an object with port (number) and nonce (string) fields representing the server's connectivity configuration.

*   The ClaudeCodeSession constructor must accept only three explicit arguments: the language model server instance, the session ID string, and a boolean indicating whether this is a new session. It must no longer accept serverConfig, initialModelId, or initialPermissionMode as constructor arguments.

*   ClaudeCodeSession must obtain server connectivity configuration (port and nonce) by calling langModelServer.getConfig() internally at the time a session is started, rather than receiving it as a constructor parameter.

*   ClaudeCodeSession must read model ID and permission mode from the session state service at request time rather than accepting them as constructor arguments. The SDK options for a new session must reflect the model and permission mode from the session state service.

*   The ClaudeCodeSession.invoke() method must accept four parameters in order: the full vscode.ChatRequest object, the response stream, an optional yieldRequested callback, and the cancellation token. It must no longer accept a separate prompt array or toolInvocationToken parameter. The prompt content must be resolved internally from the request object.

*   When ClaudeCodeSession.invoke() is called after the session has been disposed, it must throw an error with the message 'Session disposed'.

*   When ClaudeCodeSession.invoke() is called with an already-cancelled cancellation token, it must reject with an error.

*   ClaudeAgentManager.handleRequest() must accept five positional parameters: claudeSessionId (string), request (vscode.ChatRequest), stream (vscode.ChatResponseStream), token (vscode.CancellationToken), and isNewSession (boolean), with an optional sixth yieldRequested callback. It must no longer accept a ChatContext as the third parameter.

*   ClaudeAgentManager.handleRequest() must return a plain vscode.ChatResult and must no longer include a claudeSessionId field in the return value.

*   When the model ID in the session state changes between invocations on the same ClaudeCodeSession, the session must call setModel on the SDK query rather than restarting the session. When the model ID is unchanged, setModel must not be called.

*   When the permission mode in the session state changes between invocations on the same ClaudeCodeSession, setPermissionMode must be called. When the permission mode is unchanged, setPermissionMode must not be called.

*   When the effort level in the session state changes between invocations on the same ClaudeCodeSession, the session must restart (a new SDK query is created). When the effort level is unchanged, the existing query is reused.

*   For new sessions, the SDK options must include a sessionId field set to the session ID. For resumed sessions (isNewSession=false), the SDK options must include a resume field set to the session ID, and sessionId must not be set.

*   When reasoning effort is set in session state, the SDK options must include an effort field with that value. When reasoning effort is not set, the effort field must be absent from the SDK options.


*   Interface details: Type: Class
Name: ClaudeCodeSession
Location: extensions/copilot/src/extension/chatSessions/claude/node/claudeCodeAgent.ts
Description: Manages a single Claude Code chat session, queuing and processing requests sequentially. Constructor takes only the language model server, session ID, and whether the session is new (remaining parameters are injected by the DI container). Model ID and permission mode are no longer passed as constructor arguments — they are read from the session state service internally.
Signature: constructor(langModelServer: ClaudeLanguageModelServer, sessionId: string, isNewSession: boolean, ...injected)

Type: Method
Name: invoke
Location: extensions/copilot/src/extension/chatSessions/claude/node/claudeCodeAgent.ts
Description: Invokes the session with a user request. Prompt content is resolved internally from the request object. The toolInvocationToken is taken from request.toolInvocationToken. Throws 'Session disposed' if the session has been disposed. Rejects with an error if the cancellation token is already cancelled.
Signature: invoke(request: vscode.ChatRequest, stream: vscode.ChatResponseStream, yieldRequested: (() => boolean) | undefined, token: vscode.CancellationToken): Promise<void>

Type: Class
Name: ClaudeAgentManager
Location: extensions/copilot/src/extension/chatSessions/claude/node/claudeCodeAgent.ts
Description: Manages multiple Claude Code sessions, creating or reusing sessions by session ID. The handleRequest method no longer accepts a ChatContext parameter and no longer returns claudeSessionId in the result.
Signature: handleRequest(claudeSessionId: string, request: vscode.ChatRequest, stream: vscode.ChatResponseStream, token: vscode.CancellationToken, isNewSession: boolean, yieldRequested?: () => boolean): Promise<vscode.ChatResult>

Type: Method
Name: getConfig
Location: extensions/copilot/src/extension/chatSessions/claude/node/claudeLanguageModelServer.ts
Description: Returns the current server configuration including port and nonce. Must be present on the ClaudeLanguageModelServer class and used internally by ClaudeCodeSession to obtain connectivity details rather than having them passed as constructor arguments.
Signature: getConfig(): { port: number; nonce: string }


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.