## Description

Ray Serve currently lacks a unified abstraction layer for handling requests and responses across different proxy types (HTTP/ASGI and gRPC). The HTTP proxy and gRPC proxy have different interfaces for accessing request metadata, determining route types, and constructing request objects to send to replicas. This makes it difficult to write shared proxy logic and maintain consistency between the two protocols.

## Expected Behavior

- There should be a common `ProxyRequest` interface that both ASGI (HTTP) and gRPC proxies can implement
- Both proxy request types should provide consistent access to:
  - Request type (http, websocket, grpc)
  - HTTP method or gRPC equivalent
  - Route path for request routing
  - Detection of special routes (`/-/routes` for listing applications, `/-/healthz` for health checks)
- Both should be able to create protocol-specific request objects (`StreamingHTTPRequest` for ASGI, `gRPCRequest` for gRPC) to send to replicas
- The gRPC proxy should be able to:
  - Extract application name, request ID, and multiplexed model ID from metadata
  - Send status codes, details, and request IDs back to clients via the gRPC context
- There should be a unified `ProxyResponse` class for handling responses including status codes, unary responses, and streaming responses

## Current Behavior

Currently, there is no unified abstraction for proxy requests and responses. Each proxy type handles requests differently without a common interface, making it harder to share logic between HTTP and gRPC proxies. Additionally, there is no `gRPCRequest` dataclass defined for sending gRPC requests from the proxy to replicas.
