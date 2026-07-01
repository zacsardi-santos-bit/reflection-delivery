I'm working on the Rust implementation of the Fory serialization library and running into several missing pieces that are blocking cross-language compatibility tests.

First, I need two string utility functions that detect whether a string can be encoded as Latin-1 and compute its Latin-1 byte length — returning a sentinel value of -1 when the string contains characters that fall outside the Latin-1 range. These should be publicly accessible from the library's meta module.

Second, the buffer layer needs a compact variable-length encoding for unsigned integers that fit in 36 bits (values up to roughly 68 billion). The existing codecs cover 32-bit and 64-bit values, but there is a gap for this 36-bit range. The encoding should use 1 byte for small values and scale up to at most 5 bytes for the maximum 36-bit value, with an exact round-trip guarantee.

Third, the compatible-mode deserializer has a correctness problem: when a field is present in the type definition but absent from the serialized bytes (as happens during schema evolution), it must fall back to the standard default value for that type. Currently that initialization is not wired up correctly, which means types used in compatible mode need to implement the standard default trait, and certain collection types with optional string elements are not handled properly.

Finally, the Java test utility for spawning external processes does not support setting a working directory, which is needed to run the cross-language integration tests from the right location.
