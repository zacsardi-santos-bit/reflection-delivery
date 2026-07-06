Refactor the gRPC message deframing layer by separating it into two distinct components: a low-level wire format decoder and a higher-level buffered deframer. Implement the following specifications to ensure independent functionality and efficient memory management.

*   Implement `GRPCMessageDecoder` as a struct conforming to `NIOSingleStepByteToMessageDecoder` with `InboundOut = [UInt8]`.
    *   Initialize with `init(maximumPayloadSize: Int, decompressor: Zlib.Decompressor? = nil)`.
    *   Define `static let metadataLength: Int` equal to 5.
    *   Decode the gRPC wire format: a 1-byte compression flag, a 4-byte big-endian payload length, and the payload bytes.
    *   Return the decoded payload as `[UInt8]`.
    *   Throw an `RPCError` with code `.resourceExhausted` if the payload length exceeds `maximumPayloadSize`.
    *   Throw an `RPCError` with code `.internalError` if a compressed message is received without a configured decompressor.
    *   Throw an `RPCError` with code `.resourceExhausted` if the compressed payload size exceeds `maximumPayloadSize`.
    *   Throw an `RPCError` with code `.resourceExhausted` if the decompressed message size exceeds `maximumPayloadSize`.
    *   Support both deflate and gzip compression algorithms via `Zlib.Decompressor`.

*   Implement `GRPCMessageDeframer` as a package-level struct.
    *   Initialize with `package init(maxPayloadSize: Int)` and `init(maxPayloadSize: Int, decompressor: Zlib.Decompressor?)`.
    *   Implement `package mutating func append(_ buffer: ByteBuffer)`.
    *   Implement `package mutating func decodeNext() throws -> [UInt8]?`.
    *   Implement `package var _readerIndex: Int?`.
    *   Ensure `decodeNext()` returns `nil` when no bytes are appended, fewer than 5 bytes are available, or insufficient payload bytes are present.
    *   Return an empty array `[]` for zero-length messages and the full payload for non-zero length messages.
    *   Handle drip-fed bytes by returning `nil` until a complete message is available, then return the decoded payload.
    *   Ensure `_readerIndex` reflects the internal buffer's reader index and resets to 0 after appending new bytes post-read.
    *   Delegate decoding logic to `GRPCMessageDecoder` internally using `decode(into:)` to append results into a `OneOrManyQueue<[UInt8]>`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.