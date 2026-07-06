Implement a new variant of the encrypted value type in the Matrix Rust SDK that uses base64-encoded strings for ciphertext and nonce instead of raw byte arrays. Add corresponding encrypt and decrypt methods to handle this format, ensuring compatibility with string-oriented storage backends. Provide conversion support between the existing byte-based and new string-based representations, including error handling for base64 decoding failures.

*   Add methods to `StoreCipher` in `crates/matrix-sdk-store-encryption/src/lib.rs`:
    *   `encrypt_value_base64_typed(&self, value: &impl Serialize) -> Result<EncryptedValueBase64, Error>`: Encrypts a serializable value to a base64 string format.
    *   `decrypt_value_base64_typed<T: DeserializeOwned>(&self, value: EncryptedValueBase64) -> Result<T, Error>`: Decrypts a base64-encoded value back to its original type.
    *   `encrypt_value_base64_data(&self, data: Vec<u8>) -> Result<EncryptedValueBase64, Error>`: Encrypts raw bytes and returns a base64 string format.
    *   `decrypt_value_base64_data(&self, value: EncryptedValueBase64) -> Result<Vec<u8>, Error>`: Decrypts a base64-encoded value back to raw bytes.

*   Introduce `EncryptedValueBase64` struct in `crates/matrix-sdk-store-encryption/src/lib.rs`:
    *   Fields: `version (u8)`, `ciphertext (String)`, `nonce (String)`.
    *   Derive `Debug`, `Serialize`, `Deserialize`, `PartialEq`, `Eq`.
    *   Implement constructor: `new(version: u8, ciphertext: &str, nonce: &str) -> Self`.

*   Implement conversions:
    *   `From<EncryptedValue> for EncryptedValueBase64`: Encode ciphertext and nonce as base64 strings using the standard alphabet without padding. Example: `[1, 2, 4]` encodes to `'AQIE'`.
    *   `TryFrom<EncryptedValueBase64> for EncryptedValue`: Decode base64 strings back to bytes. Return `EncryptedValueBase64DecodeError` if decoding fails or nonce length is incorrect (must be 24 bytes).

*   Introduce `EncryptedValueBase64DecodeError` enum in `crates/matrix-sdk-store-encryption/src/lib.rs`:
    *   Variants: `DecodeError(base64::DecodeError)`, `IncorrectNonceLength(usize)`.
    *   Implement `Display` to forward base64 error messages and format nonce length errors.
    *   Implement `From<base64::DecodeError>`, `From<Vec<u8>>` (using length), and `From<EncryptedValueBase64DecodeError>` for `Error`.

*   Ensure base64 encoding and decoding use the standard alphabet without padding. Add `base64` crate as a dependency.

*   Maintain existing tests in the `tests` module and add new tests for conversion roundtrips and error handling.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.