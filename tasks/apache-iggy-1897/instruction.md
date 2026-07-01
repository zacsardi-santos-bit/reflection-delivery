Update the Go client library to align with the server's current wire protocol by implementing a new message format and serialization logic. Ensure that the client can correctly send and receive messages with the server using the updated protocol.

*   Implement the `IggyMessage` struct in `foreign/go/contracts/messages.go`:
    *   Fields: `Header` (type `MessageHeader`), `Payload` (`[]byte`), `UserHeaders` (`[]byte`).

*   Define the `MessageHeader` struct in `foreign/go/contracts/message_header.go`:
    *   Fields: `Checksum` (`uint64`), `Id` (`uuid.UUID`), `Offset` (`uint64`), `Timestamp` (`uint64`), `OriginTimestamp` (`uint64`), `UserHeaderLength` (`uint32`), `PayloadLength` (`uint32`).
    *   Constant: `MessageHeaderSize` with a value of 56.

*   Implement methods and functions for `MessageHeader`:
    *   `ToBytes() []byte`: Serialize `MessageHeader` to a 56-byte little-endian byte slice.
    *   `MessageHeaderFromBytes(data []byte) (*MessageHeader, error)`: Deserialize a `MessageHeader` from a 56-byte slice, returning an error if the input length is not 56.

*   Implement functions for creating `IggyMessage`:
    *   `NewIggyMessage(id uuid.UUID, payload []byte) IggyMessage`: Create an `IggyMessage` without user headers.
    *   `NewIggyMessageWithHeaders(id uuid.UUID, payload []byte, userHeaders map[HeaderKey]HeaderValue) IggyMessage`: Create an `IggyMessage` with serialized user headers.

*   Implement `GetHeadersBytes(headers map[HeaderKey]HeaderValue) []byte` in `foreign/go/contracts/user_headers.go` to serialize header maps to bytes.

*   Update `SendMessagesRequest` and `FetchMessagesResponse` structs in `foreign/go/contracts/messages.go`:
    *   `SendMessagesRequest.Messages` field must be `[]IggyMessage`.
    *   `FetchMessagesResponse.Messages` field must be `[]IggyMessage`.
    *   `FetchMessagesResponse.PartitionId` and `MessageCount` must be `uint32`.

*   Modify `Consumer` struct:
    *   `Id` must be of type `Identifier`, supporting both numeric and string forms.

*   Update serialization methods:
    *   `TcpFetchMessagesRequest.Serialize() []byte` in `foreign/go/binary_serialization/fetch_messages_request_serializer.go` must serialize `Consumer.Id` as an `Identifier`.
    *   `TcpSendMessagesRequest.Serialize(compression IggyMessageCompression) []byte` in `foreign/go/binary_serialization/send_messages_request_serializer.go` must follow the specified byte format, accepting `MESSAGE_COMPRESSION_NONE`.

*   Ensure message comparison checks that the receiver's `Header.Id`, `Payload`, and `UserHeaders` match the sender's.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.