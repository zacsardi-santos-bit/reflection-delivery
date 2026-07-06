# Add Cache Key Computation for Request Templates

## Description

The system makes outgoing requests using three different protocols—plain HTTP, GraphQL, and gRPC—but currently has no standard way to compute a unique identifier for a given request based on its parameters. This makes it impossible to reliably cache responses, because the cache has no way to recognize when two requests are identical or when they differ.

We need a common abstraction that all request template types can implement to compute a deterministic cache key at runtime. The key should incorporate all request-identifying information: the target URL, any request headers (from both the template and the request context), and the request body. Two requests that differ in any of these components must produce different keys; two identical requests must always produce the same key.

## Expected Behavior

- A shared interface allows any request type to compute a numeric (64-bit unsigned integer) cache key given a context
- HTTP request templates produce distinct keys when the URL, headers, or body differ
- For body-based keys, the cache key depends on the *rendered* body content, not the template string itself—so two different templates that render to the same string should produce the same key
- GraphQL request templates produce distinct keys for requests that result in different rendered queries
- gRPC request templates produce distinct keys when the rendered body or URL differs

## Why This Matters

Without this capability, the caching layer cannot correctly look up prior results. When two requests are logically identical, the cache can serve a stored result; when they differ, a fresh network call must be made. Getting the key computation right—especially the collision-free guarantee—is essential for correctness.
