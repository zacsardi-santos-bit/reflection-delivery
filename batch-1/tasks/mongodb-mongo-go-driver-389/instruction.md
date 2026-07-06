Update the MongoDB Go driver's TLS CA certificate handling to load all PEM certificates from a specified file into the trusted pool and provide consistent error messages for invalid CA files. Implement the following requirements to ensure proper functionality and error reporting.

*   Implement the function `addCACertFromFile(cfg *tls.Config, file string) error` in `mongo/options/clientoptions.go`.
    *   Ensure it reads a PEM file and loads all valid PEM certificate blocks into `cfg.RootCAs`.
    *   Return an error with the message "the specified CA file does not contain any valid certificates" if the file is empty, contains no certificate blocks, or has malformed data.

*   Ensure all certificates in a TLS CA file are loaded:
    *   Load all certificates into the `RootCAs` pool when a file contains multiple PEM-encoded certificates.
    *   Ensure the `RootCAs` pool contains subjects matching every certificate in the file.

*   Handle invalid CA file scenarios:
    *   Record an error with the message "the specified CA file does not contain any valid certificates" when:
        *   The CA file is empty.
        *   The CA file contains no certificate blocks (e.g., only a private key).
        *   The CA file contains malformed or non-PEM data.

*   Ensure the presence of the following testdata files in `mongo/options/testdata/`:
    *   `ca-with-intermediates.pem` — a PEM file containing three certificates followed by a private key.
    *   `ca-with-intermediates-first.pem` — a PEM file containing the first certificate from `ca-with-intermediates.pem`.
    *   `ca-with-intermediates-second.pem` — a PEM file containing the second certificate from `ca-with-intermediates.pem`.
    *   `ca-with-intermediates-third.pem` — a PEM file containing the third certificate from `ca-with-intermediates.pem`.
    *   `empty-ca.pem` — an empty file (0 bytes).
    *   `malformed-ca.pem` — a file containing non-PEM plaintext content.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.