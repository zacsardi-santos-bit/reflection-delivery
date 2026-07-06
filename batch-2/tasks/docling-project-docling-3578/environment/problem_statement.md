## Description

The service client's high-level document conversion methods always expect the converted document to be returned inline in the server's response body. When the server is configured to use external artifact storage, it returns temporary download URLs instead of embedding the document data directly. Currently, the client cannot handle this mode at all — it only knows how to submit a request expecting an inline result and cannot attempt the external-artifact path or fall back gracefully.

## Expected Behavior

- When the client submits a conversion request, it should first try the external-artifact (pre-signed URL) path. If the server does not support it, the client should automatically retry using the traditional inline response approach.
- When the server provides download URLs, the client should fetch the most complete artifact available: a resource bundle (a ZIP file containing the document and all associated images) is preferred over a standalone document JSON file.
- After downloading a resource bundle, the client should extract it and embed all referenced images directly into the in-memory document so callers receive a fully self-contained result.
- The batch conversion method should also support this pre-signed URL materialization path, returning results in the original input order.
- Security safeguards must be enforced: download URLs pointing to private or internal network addresses must be refused (to prevent the client from acting as a network proxy), and image references inside bundles that point outside the bundle's own directory must also be refused (to prevent local file reads).
- Failures at any step — server-side conversion errors, download failures, or security violations — must surface cleanly as conversion failures with descriptive error messages rather than raising unexpected exceptions.

## Why This Matters

Without this capability, users running a service instance with external artifact storage configured cannot use the high-level client API at all — conversion results are inaccessible. With this change, the client automatically handles both server configurations transparently, and the added security guardrails protect users from a misconfigured or compromised service redirecting downloads to internal infrastructure.
