I'm working on the chat sessions extension API in VS Code and I need to add proper support for forking contributed chat sessions.

*   The IChatSessionRequestHistoryItem type must be exported from chatSessionsService.ts as a type alias that extracts request-type items from IChatSessionHistoryItem (i.e., items where type === 'request'). It must have fields: type ('request'), id (string), prompt (string), participant (string), and optional fields command, variableData, and modelId.

*   The IChatSessionRequestHistoryItemDto type must be exported from extHost.protocol.ts as an extraction of request-type items from IChatSessionHistoryItemDto.

*   ChatSessionDto in extHost.protocol.ts must include a new boolean field hasForkHandler. This field must be true when the session item controller for the session's scheme has a forkHandler set, or when the session object itself has a forkHandler set.

*   The ExtHostChatSessionsShape interface in extHost.protocol.ts must declare a new method $forkChatSession(providerHandle: number, sessionResource: UriComponents, request: IChatSessionRequestHistoryItemDto | undefined, token: CancellationToken): Promise<Dto<IChatSessionItem>>.

*   ObservableChatSession in mainThreadChatSessions.ts must have an optional forkSession property with signature: (request: IChatSessionRequestHistoryItem | undefined, token: CancellationToken) => Promise<IChatSessionItem>. During initialization, when sessionContent.hasForkHandler is true and forkSession is not already set, forkSession must be assigned a function that calls proxy.$forkChatSession(providerHandle, sessionResource, requestDto, token) and returns the revived result.

*   When mapping an IChatSessionRequestHistoryItem to a DTO for the $forkChatSession call, the DTO must include: type: 'request', id: request.id, prompt: request.prompt, participant: request.participant, command: request.command (which may be undefined), variableData: undefined, modelId: request.modelId (which may be undefined).

*   The result from proxy.$forkChatSession must be passed through revive() before being returned, so that URI fields (resource, changes[].uri, changes[].originalUri) are revived to proper URI instances.

*   ExtHostChatSessions must implement $forkChatSession(handle, sessionResource, request, token). When called, it must prefer the controller's forkHandler over the session's deprecated forkHandler. If the controller has a forkHandler, it is called with (sessionResource, ChatRequestTurn, token) and the session's forkHandler is not called. If no controller forkHandler exists, the session's forkHandler is used as a fallback.

*   When $forkChatSession is invoked, a ChatRequestTurn must be constructed from the request DTO and passed to the fork handler. The ChatRequestTurn must be constructed with: prompt (request.prompt), command (request.command), empty references array [], participant (request.participant), empty tools array [], undefined context, id (request.id), and modelId (request.modelId).

*   IChatSessionsService must declare two new methods: sessionSupportsFork(sessionResource: URI): boolean, and forkChatSession(sessionResource: URI, request: IChatSessionRequestHistoryItem | undefined, token: CancellationToken): Promise<IChatSessionItem>.

*   MockChatSessionsService must implement sessionSupportsFork(_sessionResource: URI): boolean returning false, and forkChatSession(_sessionResource: URI, _request: IChatSessionRequestHistoryItem | undefined, _token: CancellationToken): Promise<IChatSessionItem> throwing new Error('Not implemented').


*   Interface details: Type: Property
Name: forkSession
Location: src/vs/workbench/api/browser/mainThreadChatSessions.ts
Signature: forkSession?: (request: IChatSessionRequestHistoryItem | undefined, token: CancellationToken) => Promise<IChatSessionItem>
Description: Optional property on ObservableChatSession. Set during initialization when sessionContent.hasForkHandler is true. Calls proxy.$forkChatSession with the provider handle, session resource, a request DTO, and the cancellation token. The result is passed through revive() to reconstruct URI instances before returning.

Type: Field
Name: hasForkHandler
Location: src/vs/workbench/api/common/extHost.protocol.ts
Signature: hasForkHandler: boolean
Description: New boolean field on ChatSessionDto. Must be true when the controller for the session scheme has a forkHandler set, or when the session object itself has a forkHandler set.

Type: Type alias
Name: IChatSessionRequestHistoryItemDto
Location: src/vs/workbench/api/common/extHost.protocol.ts
Signature: IChatSessionRequestHistoryItemDto = Extract<IChatSessionHistoryItemDto, { type: 'request' }>
Description: DTO type for a request history item. Extracted from IChatSessionHistoryItemDto. Fields include: type ('request'), id (string), prompt (string), participant (string), command (optional), variableData (optional), modelId (optional).

Type: Method
Name: $forkChatSession
Location: src/vs/workbench/api/common/extHost.protocol.ts (ExtHostChatSessionsShape interface)
Signature: $forkChatSession(providerHandle: number, sessionResource: UriComponents, request: IChatSessionRequestHistoryItemDto | undefined, token: CancellationToken): Promise<Dto<IChatSessionItem>>
Description: New method on ExtHostChatSessionsShape. Called from the main thread to trigger a fork in the extension host. Returns the forked session item DTO.

Type: Method
Name: $forkChatSession
Location: src/vs/workbench/api/common/extHostChatSessions.ts (ExtHostChatSessions class)
Signature: $forkChatSession(handle: number, sessionResourceComponents: UriComponents, request: IChatSessionRequestHistoryItemDto | undefined, token: CancellationToken): Promise<ReturnType<typeof typeConvert.ChatSessionItem.from>>
Description: Implementation on ExtHostChatSessions. When called, prefers the controller's forkHandler over the session's deprecated forkHandler. The controller's forkHandler is called with (sessionResource, ChatRequestTurn, token). The session's forkHandler is used as fallback if no controller forkHandler exists. Constructs a ChatRequestTurn from the request DTO to pass to the handler.

Type: Type alias
Name: IChatSessionRequestHistoryItem
Location: src/vs/workbench/contrib/chat/common/chatSessionsService.ts
Signature: IChatSessionRequestHistoryItem = Extract<IChatSessionHistoryItem, { type: 'request' }>
Description: Exported type alias for a request-type chat session history item. Has fields: type ('request'), id (string), prompt (string), participant (string), and optional command, variableData, modelId fields.

Type: Method
Name: sessionSupportsFork
Location: src/vs/workbench/contrib/chat/common/chatSessionsService.ts (IChatSessionsService interface)
Signature: sessionSupportsFork(sessionResource: URI): boolean
Description: Returns whether the loaded session at the given resource supports forking conversations.

Type: Method
Name: forkChatSession
Location: src/vs/workbench/contrib/chat/common/chatSessionsService.ts (IChatSessionsService interface)
Signature: forkChatSession(sessionResource: URI, request: IChatSessionRequestHistoryItem | undefined, token: CancellationToken): Promise<IChatSessionItem>
Description: Forks a contributed chat session from the given request point. Throws if the session does not support forking.

Type: Method
Name: sessionSupportsFork
Location: src/vs/workbench/contrib/chat/test/common/mockChatSessionsService.ts (MockChatSessionsService class)
Signature: sessionSupportsFork(_sessionResource: URI): boolean
Description: Mock implementation. Returns false.

Type: Method
Name: forkChatSession
Location: src/vs/workbench/contrib/chat/test/common/mockChatSessionsService.ts (MockChatSessionsService class)
Signature: forkChatSession(_sessionResource: URI, _request: IChatSessionRequestHistoryItem | undefined, _token: CancellationToken): Promise<IChatSessionItem>
Description: Mock implementation. Throws Error('Not implemented').


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.