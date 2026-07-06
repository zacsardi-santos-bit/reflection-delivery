I'm working on a Rust crypto library that exposes an FFI layer to other languages. I've found some memory ownership issues that need to be fixed before we can add memory sanitizer checks to CI.

The main problem is that some functions allocate a string on the heap and return a pointer to it, but the return type is declared as read-only (const). This is wrong because the caller needs to own the memory in order to free it. Specifically, the function that generates a random UUID returns a read-only pointer even though it uses an ownership-transferring allocation internally — so callers using proper ownership semantics get a compile error.

On top of that, the bit reader utility has a create function but no corresponding delete/destroy function. Every bit reader that gets created leaks its memory because there's no way to clean it up. The tests for these utilities need to call the destroy function after use, but it doesn't exist yet.

Could you fix the UUID function to return a mutable (owned) pointer, and add the missing destroy function for bit readers? Also, the base32 encoding and decoding functions should accept a read-only pointer for the alphabet argument, since they only read it — that needs to match what callers are actually passing.
