## Description

The TPM2 library needs two important additions: the ability to load externally-generated keys into the TPM, and the ability to certify keys using the TPM's attestation mechanism. Currently, there is no way for developers to bring an externally-created key into the TPM for use, nor is there any API to have the TPM produce an attestation statement proving a key is held within it.

Additionally, the existing key management functions (for creating primary keys, creating child keys, loading keys, activating credentials, and evicting keys from persistent storage) use a simplified authorization model that does not distinguish between the authorization required for the parent or hierarchy and the authorization required for the key being operated on. These should be separated into distinct parameters.

## Expected Behavior

- Developers can load an externally-generated RSA key pair into the TPM, providing the public key parameters and the private key material, and get back a TPM handle they can use.
- Developers can certify a key held in the TPM: given a subject key handle and a signing key handle, the TPM produces an attestation blob and a signature over that blob. The signature can be verified using standard RSA signature verification against the signer's public key.
- Key management functions accept separate authorization passwords for the parent/hierarchy and the key itself, allowing more precise access control.

## Why This Matters

These capabilities are fundamental for TPM-based attestation workflows. Without the ability to load external keys and produce attestation statements, developers cannot implement common TPM use cases like remote attestation or key certification. The cleaner authorization parameter separation also avoids potential security mistakes where a single password might be incorrectly applied in both contexts.
