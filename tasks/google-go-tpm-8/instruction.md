Implement the ability to load externally-generated RSA keys into the TPM and certify keys using the TPM's attestation mechanism. Update existing key management functions to separate authorization parameters for enhanced security.

Requirements:
*   Define `defaultPassword` as the raw byte string `"\x01\x02\x03\x04"` and `emptyPassword` as `""`.
*   Update the following functions to accept separate authorization passwords:
    *   `CreatePrimary`: Accepts `parentPassword` and `ownerPassword` before `params`.
    *   `CreateKey`: Accepts `parentPassword` and `ownerPassword` before `params`.
    *   `Load`: Accepts `parentPassword`.
    *   `ActivateCredential`: Accepts `activatePassword` and `keyPassword`.
    *   `EvictControl`: Accepts `ownerPassword`.
*   Implement `LoadExternal` function:
    *   Signature: `LoadExternal(rw io.ReadWriter, rp RSAParams, private Private, hierarchy tpmutil.Handle) (tpmutil.Handle, Name, error)`
    *   Load an externally-generated RSA key pair into the TPM.
    *   Accept `RSAParams` and `Private` structs and a hierarchy handle.
    *   Return the TPM object handle, name, and error.
*   Implement `Certify` function:
    *   Signature: `Certify(rw io.ReadWriter, objectPassword, signingPassword string, object, signer tpmutil.Handle, qualifyingData []byte) ([]byte, []byte, error)`
    *   Certify a key held in the TPM.
    *   Return attestation data and signature, verifiable with RSA PKCS1v15 using SHA-256.
*   Define `RSAParams` struct with fields: `EncAlg`, `HashAlg`, `Attributes`, `SymAlg`, `Scheme`, `SchemeHash`, `ModSize`, `Exp`, `Modulus`.
*   Define `Private` struct with fields: `Type`, `Sensitive`.
*   Ensure the default RSA key size in `defaultKeyParams` is 2048 bits.
*   Provide the following constants:
    *   `HandleNull`
    *   `FlagSign`
    *   `FlagSensitiveDataOrigin`
    *   `FlagUserWithAuth`
    *   `FlagSignerDefault`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.