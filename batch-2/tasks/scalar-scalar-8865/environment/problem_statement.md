## Description

The API client is unable to handle responses with custom or non-standard content types. When a server returns a response encoded in a proprietary format (such as a binary serialization format), the client has no way to decode it — it only supports built-in types like JSON, plain text, and binary blobs. We need a plugin mechanism so that developers can register custom decoders for specific MIME types and have them automatically used when those content types appear in responses.

Additionally, when a server response is missing a content-type header entirely, the client currently behaves unpredictably or fails to process the response correctly. The client should default gracefully to treating the response as plain text when no content-type is provided.

## Expected Behavior

- Developers can register response body decoders (plugins) for specific MIME types, including wildcard patterns
- When a response arrives with a MIME type matching a registered plugin decoder, the decoder is called and its output is returned as the response data
- MIME type matching in plugin resolution must be case-insensitive
- When multiple plugins register handlers for the same MIME type, the first registered handler wins
- Plugins that don't register response body handlers are skipped gracefully
- When a response is missing a content-type header, the system defaults to plain text for both MIME type resolution and plugin decoder lookup
- The request execution layer must accept and forward the list of active plugins when dispatching requests

## Why This Matters

Without this plugin system, users working with APIs that return binary or custom-encoded data are forced to handle decoding themselves outside the client, losing the benefits of the integrated response handling pipeline. The missing content-type fallback also causes silent failures for APIs that omit this header.
