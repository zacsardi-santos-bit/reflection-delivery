I'm working on refactoring the WebSocket reconnection and health-check infrastructure in the marimo frontend.

*   The RuntimeManager class must expose a probeHealth() method that fetches the health endpoint and returns true when the response is ok, false when the response is not ok, and false when the fetch throws an error. This method must NOT update config.url or the httpURL getter's hostname, even when the health response indicates a redirect.

*   The RuntimeManager class must expose a reconcileFromHealth() method that fetches the health endpoint and, when the response indicates a redirect, updates config.url by stripping /health from the pathname while preserving any query parameters. It returns true on a successful health check and false on failure.

*   The RuntimeManager methods waitForHealthy() and setDOMBaseUri() must use reconcileFromHealth() (not probeHealth()) as the health-checking call — reconcileFromHealth() is the method that drives URL reconciliation and is used for retries.

*   The RuntimeManager.formatHttpURL() method must accept a single object argument with named fields: path (string), searchParams (URLSearchParams), and restrictToKnownQueryParams (boolean), rather than positional arguments.

*   The classifyCloseEvent function must accept only a single argument — the close event object with an optional reason string — and must no longer accept a second retry-state parameter containing retryCount or maxRetries.

*   The classifyCloseEvent function must handle the reason string 'MARIMO_TRANSPORT_EXHAUSTED' as a terminal 'gave-up' case, returning { kind: 'gave-up', status: { state: WebSocketState.CLOSED, code: WebSocketClosedReason.KERNEL_DISCONNECTED, reason: 'kernel not found' } }.

*   A new WsTransport class must be created in frontend/src/core/websocket/transports/ws.ts. It wraps a reconnecting WebSocket and intercepts close events. When the inner socket's retryCount has reached MAX_RETRIES, the transport must rewrite the close event's reason to TRANSPORT_EXHAUSTED_REASON before passing it to registered listeners — this rewrite applies even when the original close event carries a server-sent reason.

*   When the inner socket's retryCount is less than MAX_RETRIES, the WsTransport must forward the original close event reason unchanged.

*   The WsTransport.addEventListener() method must deduplicate registrations: if the same callback is added more than once, it must only fire once per close event.

*   The WsTransport.removeEventListener() method must fully unregister a callback with a single call, even if that callback was previously added multiple times. After removal, no orphaned wrapper listeners must remain on the underlying socket.

*   The constants MAX_RETRIES (numeric) and TRANSPORT_EXHAUSTED_REASON (string equal to 'MARIMO_TRANSPORT_EXHAUSTED') must be exported from frontend/src/core/websocket/transports/ws.ts.


*   Interface details: Type: Class
Name: RuntimeManager
Location: frontend/src/core/runtime/runtime.ts
Description: Manages the connection to the marimo backend runtime. The class must expose the following methods and getter:
Signature: probeHealth(): Promise<boolean>
  - Fetches the health endpoint and returns true if the response is ok, false if the response is not ok or the fetch throws. Must NOT mutate config.url even when the response is redirected.
Signature: reconcileFromHealth(): Promise<boolean>
  - Fetches the health endpoint, updates config.url on redirect (stripping /health from the pathname while preserving query params), and returns true on success or false on failure.
Signature: formatHttpURL({ path, searchParams, restrictToKnownQueryParams }: { path: string, searchParams: URLSearchParams, restrictToKnownQueryParams: boolean }): URL
  - Accepts a single object argument with named fields (not positional arguments).
Getter: httpURL: URL
  - Returns the current base URL as a URL object (with accessible .hostname, etc.)

Type: Function
Name: classifyCloseEvent
Location: frontend/src/core/websocket/useMarimoKernelConnection.ts
Signature: classifyCloseEvent(event: { reason?: string }): CloseDecision
Description: Classifies a WebSocket close event into a decision about what to do next. The function takes only the close event object — it no longer accepts a second retry-state parameter. When the reason is "MARIMO_TRANSPORT_EXHAUSTED", the function must return { kind: "gave-up", status: { state: WebSocketState.CLOSED, code: WebSocketClosedReason.KERNEL_DISCONNECTED, reason: "kernel not found" } }. Empty or undefined reasons still return { kind: "retry" }.

Type: Class
Name: WsTransport
Location: frontend/src/core/websocket/transports/ws.ts
Description: A WebSocket transport wrapper around a reconnecting WebSocket. Intercepts close events and rewrites the reason to TRANSPORT_EXHAUSTED_REASON when the inner socket's retryCount has reached MAX_RETRIES.
Signature: constructor(urlFactory: () => string)
  - Takes a URL factory function.
Signature: addEventListener(event: string, callback: (e: unknown) => void): void
  - Registers an event listener with deduplication: adding the same callback reference twice must not cause it to fire twice. Internally uses a wrapper for "close" events to intercept and rewrite the reason on exhaustion.
Signature: removeEventListener(event: string, callback: (e: unknown) => void): void
  - Unregisters the callback. A single call must fully unregister even if the callback was added multiple times. Must also remove the internal wrapper from the underlying socket (no orphaned listeners remain).
Property: inner (accessible as (transport as any).inner)
  - The underlying ReconnectingWebSocket instance. Has retryCount: number and readyState: number.

Type: Constant
Name: MAX_RETRIES
Location: frontend/src/core/websocket/transports/ws.ts
Description: Exported numeric constant representing the maximum number of reconnection attempts before the transport is considered exhausted.

Type: Constant
Name: TRANSPORT_EXHAUSTED_REASON
Location: frontend/src/core/websocket/transports/ws.ts
Description: Exported string constant with value "MARIMO_TRANSPORT_EXHAUSTED". Used as the rewritten close reason when the retry budget is exhausted, and recognized by classifyCloseEvent as the "gave-up" signal.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.