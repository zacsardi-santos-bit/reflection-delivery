Implement the NIP-05 identity resolution logic to decouple it from HTTP networking and make it available by default. Ensure that the parsing and profile extraction can be performed without network calls, allowing developers to use their own HTTP clients.

Requirements:

*   Implement `Nip05Address::parse`:
    *   Accept a string in "name@domain" format (e.g., "0xtr@oxtr.dev").
    *   Return `Ok(Nip05Address)` for valid inputs.
    *   Return an error for malformed inputs.
*   Implement `Nip05Profile::from_raw_json`:
    *   Accept a `&Nip05Address` and a raw JSON string.
    *   Parse the JSON and look up the address name in the JSON 'names' object.
    *   Return `Ok(Nip05Profile)` with the `public_key` field set to the `PublicKey` corresponding to the hex-encoded public key string in the JSON.
*   Ensure `Nip05Profile` has:
    *   A public field `public_key` of type `PublicKey` that holds the resolved key from the NIP-05 JSON response.
*   Ensure the JSON format accepted by `Nip05Profile::from_raw_json` follows the NIP-05 specification:
    *   A 'names' object mapping name strings to hex-encoded public key strings (e.g., `{"names": {"0xtr": "b2d670de53b27691c0c3400225b65c35a26d06093bcc41f48ffc71e0907f9d4a"}}`).
*   Make both `Nip05Address` and `Nip05Profile` accessible via `use nostr::prelude::*` without requiring any optional cargo feature flags.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.