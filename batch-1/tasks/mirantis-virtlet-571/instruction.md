Implement cancellation support for image download and pull operations in the virtlet system. Ensure that both operations can be interrupted with a cancellation signal and return appropriate errors. Additionally, refactor TLS certificate generation utilities into a shared package for reuse.

*   Update the Downloader interface:
    *   Modify the `DownloadFile` method to accept `context.Context` as its first parameter: `DownloadFile(ctx context.Context, endpoint Endpoint, w io.Writer) error`.
    *   Ensure that when the context is cancelled during a download, the method stops and returns an error containing 'context canceled'.

*   Update the NewDownloader function:
    *   Ensure it returns a Downloader that can download files over HTTP and HTTPS.
    *   For HTTPS, support endpoints with TLS configuration using CA certificates.

*   Update the ImageStore interface:
    *   Modify the `PullImage` method to accept `context.Context` as its first parameter: `PullImage(ctx context.Context, name string, translator ImageTranslator) (string, error)`.
    *   Ensure that when the context is cancelled during a pull, the method returns an error containing 'context canceled'.

*   Update the ImageFileStore implementation:
    *   Modify the `PullImage` method to accept `context.Context` and pass it to the downloader's `DownloadFile` call.

*   Create a new file `pkg/utils/testing/https.go`:
    *   Implement `GenerateCert(t *testing.T, isCA bool, host string, signer *x509.Certificate, key *rsa.PrivateKey) (*x509.Certificate, *rsa.PrivateKey)` for generating TLS certificates.
    *   Implement `EncodePEMCert(cert *x509.Certificate) string` to encode certificates to PEM strings.
    *   Implement `EncodePEMKey(key *rsa.PrivateKey) string` to encode private keys to PEM strings.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.