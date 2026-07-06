I'm working on the HTTP/2 transport layer for a gRPC library and I need to add two new components to the core module.

The first is a small FIFO queue utility that is optimized for the common case where only one element is held at a time, avoiding a heap allocation in that situation and only falling back to a heap-based container when multiple elements are present simultaneously. It should conform to the standard collection protocol so callers can iterate over it and access elements by index. It also needs append and pop operations that respect FIFO ordering, plus the usual empty and count checks. Importantly, it should continue to work correctly even after the backing has been switched from single-element to multi-element mode and then become empty again.

The second is a message framer that queues raw byte arrays and, when asked, produces a single output buffer containing all the queued messages formatted according to the gRPC wire protocol. Each message in the output must be prefixed with a one-byte compression flag followed by a four-byte message length before the actual bytes. When multiple messages have been queued they should all be packed into one buffer per flush. After the buffer is returned the framer should indicate there is nothing more to deliver on the next request.

Both components should live in the HTTP/2 core module of the library.
