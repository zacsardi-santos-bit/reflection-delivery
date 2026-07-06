## Description

The store encryption library currently represents encrypted values using raw byte arrays for both the ciphertext and the nonce. While this is fine for many storage backends, certain environments — particularly browser-based storage systems — work more efficiently with string data rather than arrays of integers. Storing large arrays of integers is both wasteful and slow in those contexts.

We need a new variant of the encrypted value type that represents the encrypted data using base64-encoded strings rather than raw byte sequences. This string-based format is more compact and compatible with string-oriented storage backends.

## Expected Behavior

- A new encrypted value type that holds the ciphertext and nonce as base64-encoded strings instead of byte arrays
- New encrypt/decrypt methods on the cipher that produce and consume this base64 string format
- Correct roundtrip: encrypting a value with the new method and then decrypting it should return the original value
- A corresponding error type that is returned when conversion from base64 back to raw bytes fails (for example, when the input is not valid base64, or when the decoded nonce has the wrong length)
- Conversion support in both directions between the raw-bytes representation and the new string-based representation

## Why This Matters

Browser-based storage backends such as IndexedDB are significantly more efficient when storing JavaScript string values compared to arrays of integers. This change enables the encrypted store to be used more efficiently in such environments by reducing the size and improving the performance of stored session data.
