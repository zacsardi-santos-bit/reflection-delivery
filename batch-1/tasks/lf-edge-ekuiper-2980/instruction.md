Implement a WebSocket server-side data source for the ekuiper stream processing engine to allow external clients to push data into the engine over WebSocket connections. Ensure the server can manage connections, handle messages, and integrate with the engine's connection management system.

*   Implement `InitGlobalServerManager` in `internal/io/http/httpserver/data_server.go` to initialize a global WebSocket/HTTP server manager that listens on a specified IP and port.
    *   Implement `ShutDown` in the same location to stop the server and release all resources.

*   Implement `RegisterWebSocketEndpoint` in `internal/io/http/httpserver/websocket_server.go` to register a WebSocket endpoint and return a pub/sub topic string.
    *   Implement `UnRegisterWebSocketEndpoint` to unregister the endpoint, cancel all active connections, and block until all connection goroutines have exited.
    *   Ensure that after a client sends a Close frame, the connection is removed from the internal map, and the `WaitGroup` unblocks.

*   Implement `createWebsocketServerConnection` in `internal/io/http/httpserver/websocketConn.go` to create a `WebsocketConnection` using a `props` map with a "datasource" key.
    *   Register the endpoint and store the returned topic in `RecvTopic`.
    *   Ensure `Ping` always returns nil and `DetachSub` calls `UnRegisterWebSocketEndpoint`.

*   Implement `CreateWebsocketConnection` in `internal/io/http/httpserver/websocketConn.go` as a public function to create a connection, delegating to `createWebsocketServerConnection`.
    *   Register this function with the modules system under the "websocket" key.

*   Implement `WebsocketSource` in `internal/io/websocket/websocket_source.go` to manage server-side WebSocket data ingestion.
    *   Implement `Provision` to validate configuration, returning an error if "datasource" does not start with "/".
    *   Implement `Connect` to fetch a `WebsocketConnection` and store `RecvTopic`.
    *   Implement `Subscribe` to register a data callback and handle incoming messages.
    *   Implement `Close` to close the consumer channel and detach the connection.

*   Implement `GetSource` in `internal/io/websocket/websocket_source.go` to return a new `WebsocketSource` instance as an `api.Source`.

*   Implement `CreateWebsocketClient` in `internal/testx/testUtil.go` to create a WebSocket client connection to `ws://{ip}:{port}{endpoint}` and return a `*websocket.Conn`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.