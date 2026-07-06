Implement utilities to handle passkey authentication compatibility with the Bitwarden browser extension. Ensure that malformed credential objects from Bitwarden are processed correctly, and prepare credential creation options with proper encoding and error handling.

*   Implement the `encodeAsBase64Url` function in `src/main/webapp/app/shared/util/base64.util.ts`.
    *   Accept a `Uint8Array` and return a URL-safe Base64 string without padding.
    *   Replace '+' with '-' and '/' with '_', and remove all trailing '=' padding.
    *   Return an empty string for an empty `Uint8Array`.
    *   Export the function from the module.

*   Define the `MalformedBitwardenCredential` interface in `src/main/webapp/app/core/user/settings/passkey-settings/entities/malformed-bitwarden-credential.ts`.
    *   Include fields: `id` (string), `rawId` (Record<string, number>), `type` (string), `authenticatorAttachment` (string).
    *   Include a `response` object with `clientDataJSON` and optional fields like `attestationObject`, `authenticatorData`, etc., as `Record<string, number>`.
    *   Include methods like `getAuthenticatorData`, `getPublicKey`, `getPublicKeyAlgorithm`, and `getTransports`.
    *   Include a `getClientExtensionResults` method returning unknown.
    *   Export the interface.

*   Implement the `getCredentialFromMalformedBitwardenObject` function in `src/main/webapp/app/core/user/settings/passkey-settings/util/bitwarden.util.ts`.
    *   Accept a `MalformedBitwardenCredential` or `null` and return a `Credential` or `null`.
    *   Return `null` for `null` input or unprocessable objects.
    *   Convert valid `MalformedBitwardenCredential` into a `Credential-like` object with fields: `id`, `type`, `rawId`, and a `response` object with `attestationObject`, `authenticatorData`, `clientDataJSON`, `publicKey`, `publicKeyAlgorithm`, and `transports`.
    *   Export the function.

*   Implement the `createCredentialOptions` function in `src/main/webapp/app/core/user/settings/passkey-settings/util/credential-option.util.ts`.
    *   Accept `PublicKeyCredentialCreationOptions` and `User` as parameters.
    *   Decode the `challenge` field using `decodeBase64url`.
    *   Set the `user` field to an object with `id` as UTF-8 encoded bytes of `user.id`, `name` as `user.email`, and `displayName` as `user.email`.
    *   Decode each `excludeCredentials` entry's `id` field using `decodeBase64url`.
    *   Set `excludeCredentials` to `undefined` if not provided in input.
    *   Set `authenticatorSelection` to `{ requireResidentKey: true, userVerification: 'preferred' }`.
    *   Throw an `Error` with message 'Invalid credential' if `user.id` or `user.email` is missing or empty.
    *   Export the function.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.