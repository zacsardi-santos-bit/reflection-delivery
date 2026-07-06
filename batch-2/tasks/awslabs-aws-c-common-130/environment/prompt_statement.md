I'm cleaning up API inconsistencies in our C byte buffer library before it gets widely deployed, since inconsistent arg ordering causes bugs when callers accidentally swap same-typed args, and naming write functions after the read-only cursor type confuses people about which type to use. A few distinct things to fix together.

First, the buffer init functions that take an allocator currently put the allocator first, but our convention is destination first, so I want the destination buffer pointer first, then the allocator, then the capacity.

Second, there's a family of write functions (raw bytes, single bytes, big-endian 16/32/64-bit ints, whole buffers, and strings) that are named after the read-only cursor type even though they write into a mutable buffer. Rename them to reflect the mutable buffer type and make them take a mutable buffer pointer as the target. The one that writes a whole buffer should take its source by value instead of by pointer. Oh and when a write fails because there isn't enough capacity, leave the destination unchanged.

Third, the encoding funcs (hex and base64, both encode and decode directions) plus the decoded-length computation helper take input as a mutable buffer pointer but only read it, so switch those to a read-only cursor.

Fourth, the string splitting functions take a mutable buffer even though they only read it, so switch to a read-only cursor and rename accordingly. The variant that takes a max split count should also get its arg order fixed so the count comes before the output list.

Also the integer serialization helpers that write 16/24/32/64-bit values into a byte array currently take the destination first and value second, which reads backwards, so flip it: value first (what to write), then destination.

And I want a new helper that makes a write-ready buffer over a pre-existing byte array, setting length to zero and capacity to the array size, separate from the existing one that treats the array as already containing data, so callers have a clean way to set up an output buffer without confusing it with a populated one.
