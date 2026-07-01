Implement a method to send encrypted messages directly to specific devices using device-to-device encryption. Ensure the method handles partial failures gracefully and is gated behind a feature flag for experimental use.

*   Implement `encrypt_and_send_raw_to_device` as a public async method on the `Encryption` struct in `crates/matrix-sdk/src/encryption/mod.rs`.
    *   Use the feature flag `experimental-send-custom-to-device` with `#[cfg(feature = "experimental-send-custom-to-device")]`.
*   Method signature: 
    *   `pub async fn encrypt_and_send_raw_to_device(&self, recipient_devices: Vec<&Device>, event_type: &str, content: Raw<AnyToDeviceEventContent>) -> Result<Vec<(OwnedUserId, OwnedDeviceId)>>`
*   Parameters:
    *   `Vec<&Device>`: List of recipient device references.
    *   `&str`: Event type (e.g., "call.keys").
    *   `Raw<AnyToDeviceEventContent>`: Raw JSON content to encrypt and send.
*   Return type:
    *   `Result<Vec<(OwnedUserId, OwnedDeviceId)>>`: 
        *   Ok variant contains a list of failed devices as 2-tuples `(OwnedUserId, OwnedDeviceId)`.
        *   An empty Vec indicates all devices received the message successfully.
*   Encryption and sending:
    *   Encrypt content using Olm for each device.
    *   Send encrypted messages via a PUT request to `/_matrix/client/r0/sendToDevice/m.room.encrypted/{txnId}`.
    *   Return `Ok` with an empty Vec if all devices are reachable and encryption succeeds.
*   Failure handling:
    *   On server errors (e.g., HTTP 500), retry the sendToDevice request up to 3 times.
    *   If retries are exhausted, add affected devices to the failures Vec.
    *   If encryption cannot be established due to unavailable one-time keys, do not call sendToDevice and include the device in the failures Vec.
    *   Both server-side delivery failures and encryption-setup failures must be reported in the same Ok-variant Vec.
*   Feature flag:
    *   Declare `experimental-send-custom-to-device` in the Cargo.toml files of matrix-sdk, matrix-sdk-base, and matrix-sdk-crypto.
    *   Ensure the feature flag chains through all relevant crates for activation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.