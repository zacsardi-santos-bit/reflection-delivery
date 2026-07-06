Implement a fix for the gossip communication layer's stream reading logic to handle malformed messages correctly. Ensure the server terminates connections with peers sending corrupted data and provide a mock implementation for testing.

*   Update the `readFromStream` method in `gossip/comm/conn.go`:
    *   Ensure that when an error occurs while parsing a received envelope, the error is sent to the `errChan` and the method immediately returns.
    *   Ensure no messages are sent to `msgChan` after encountering a parsing error.

*   Modify the `newConnection` function in `gossip/comm/conn.go`:
    *   Accept parameters for a gossip client, a gRPC client connection, a stream, `CommMetrics`, and a `ConnConfig` struct.
    *   Return a connection object configured with the provided parameters.
    *   Ensure `ConnConfig` struct includes `RecvBuffSize` and `SendBuffSize` integer fields.

*   Implement server behavior for handling malformed messages:
    *   Ensure the server forcibly closes the stream when a connected peer sends repeated malformed envelope data.
    *   Ensure the sending peer receives an end-of-stream (EOF) error upon connection termination.

*   Provide a mock implementation for testing:
    *   Define a `MockStream` interface in `gossip/comm/conn.go` that extends `proto.Gossip_GossipStreamClient`.
    *   Implement a `MockStream` struct in `gossip/comm/mocks/mock_stream.go` using `testify/mock`:
        *   Implement methods: `CloseSend() error`, `Context() context.Context`, `Header() (metadata.MD, error)`, `Recv() (*gossip.Envelope, error)`, `RecvMsg(m interface{}) error`, `Send(*gossip.Envelope) error`, `SendMsg(m interface{}) error`, `Trailer() metadata.MD`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.