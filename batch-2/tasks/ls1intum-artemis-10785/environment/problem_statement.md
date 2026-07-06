## Description

Users who have the Bitwarden browser extension installed are unable to register or authenticate using passkeys in our application. The Bitwarden plugin returns credential objects in a non-standard format that our code cannot process: instead of using proper binary arrays, it uses plain objects with numeric string keys. This causes serialization to fail, breaking the entire passkey flow for Bitwarden users.

Additionally, the credential creation options logic is currently embedded directly in the passkey settings component rather than being a reusable utility, and there is no URL-safe Base64 encoding function available in the shared utilities.

## Expected Behavior

- A new shared utility function should encode binary data (byte arrays) as URL-safe Base64 strings without padding characters, for use in WebAuthn credential processing.
- A new utility should convert the malformed Bitwarden credential format into a proper credential object, returning null for null or unprocessable input.
- A new utility should prepare the credential creation options from server-provided data and a user object, decoding binary fields from Base64, populating the user identity fields, and applying standard authenticator selection settings.
- The credential options utility must reject invalid user data with a clear error and handle the case where optional credential exclusion lists are absent.

## Why This Matters

Bitwarden is one of the most widely used password managers, and its browser extension is common among security-conscious users — who are also likely to adopt passkeys. Silently failing when these users attempt to register or log in with passkeys provides a poor experience and undermines the usefulness of the passkey feature.
