I'm working on the store encryption crate in the Matrix Rust SDK. The current encrypted value type stores ciphertext and nonce as raw byte arrays, but I need a new variant that stores them as base64-encoded strings instead. This is needed because browser-based storage backends are much more efficient with strings than with arrays of integers.

I need to add a new public struct to the store encryption library that holds encrypted data using base64 strings for the ciphertext and nonce fields, along with corresponding new encrypt and decrypt methods on the cipher. The new encrypt method should accept any serializable value, encrypt it, and return the base64 string form. The new decrypt method should accept that same base64 form and return the decrypted, deserialized value. Encrypting and then decrypting a value should reproduce the original value exactly.

There should also be lower-level variants of these methods that work directly with raw byte vectors instead of typed, serializable values.

I also need conversion support in both directions between the existing raw-bytes encrypted value type and the new base64 string encrypted value type. The conversion from base64 back to raw bytes can fail (for example, if the input contains invalid base64 or if the decoded nonce has the wrong length), and there should be a dedicated error type for these failures that provides a clear error message.

The base64 encoding should use the standard alphabet without padding characters.
