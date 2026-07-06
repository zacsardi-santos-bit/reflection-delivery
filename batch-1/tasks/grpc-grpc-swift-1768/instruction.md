Implement two components in the GRPCHTTP2Core module: a FIFO queue utility and a message framer. The queue should optimize for single-element storage without heap allocation, while the framer should format messages according to the gRPC wire protocol.

Requirements:

* Implement `GRPCMessageFramer` as a struct in `Sources/GRPCHTTP2Core/GRPCMessageFramer.swift`:
    * Initialize with no arguments.
    * Provide a mutating `append(_ bytes: [UInt8], compress: Bool)` method to queue messages.
    * Provide a mutating `next() throws -> ByteBuffer?` method:
        * Return all queued messages framed into a single `ByteBuffer`.
        * Return `nil` if there are no pending messages.
    * Frame each message with:
        * A 1-byte `UInt8` compression flag (0 for uncompressed).
        * A 4-byte `UInt32` big-endian message length.
        * The raw message bytes.
    * Coalesce multiple messages into one `ByteBuffer` when `next()` is called.
    * Ensure `next()` returns `nil` after draining all messages.

* Implement `OneOrManyQueue` as an internal generic struct in `Sources/GRPCHTTP2Core/OneOrManyQueue.swift`:
    * Conform to the `Collection` protocol with `Int` index type.
    * Expose `isEmpty` (Bool) and `count` (Int) properties:
        * Ensure a freshly initialized queue has `isEmpty == true` and `count == 0`.
    * Expose `startIndex` (always 0) and `endIndex` (equals the current count).
    * Support index-based subscript access (`[Int] -> Element`) and `index(after:)`.
    * Provide a mutating `append(_ element: Element)` method.
    * Provide a mutating `pop() -> Element?` method:
        * Return elements in FIFO order.
        * Return `nil` when the queue is empty.
    * Maintain correct operation after transitioning to multi-element backing and back.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.