Implement a unified abstraction layer for handling proxy requests and responses in Ray Serve. Create a `ProxyRequest` base class and extend it for ASGI and gRPC proxies to provide consistent access to request metadata and route detection. Also, implement a `ProxyResponse` class and a `gRPCRequest` dataclass for handling responses and gRPC requests, respectively.

*   Create an abstract base class `ProxyRequest` in `ray/serve/_private/proxy_request_response.py` with the following abstract properties:
    *   `request_type` -> str
    *   `method` -> str
    *   `route_path` -> str
    *   `is_route_request` -> bool
    *   `is_health_request` -> bool

*   Implement `ASGIProxyRequest` class extending `ProxyRequest`:
    *   Constructor: `ASGIProxyRequest(scope: Scope, receive: Receive, send: Send)`
    *   Properties:
        *   `request_type` returns `scope["type"]` or `""`
        *   `method` returns `scope["method"].upper()` or `"WEBSOCKET"`
        *   `route_path` returns path with `root_path` prefix removed
        *   `is_route_request` returns `True` if `route_path == "/-/routes"`
        *   `is_health_request` returns `True` if `route_path == "/-/healthz"`
        *   `client` returns `scope["client"]` or `""`
        *   `root_path` returns `scope["root_path"]` or `""`
        *   `path` returns `scope["path"]` or `""`
        *   `headers` returns `scope["headers"]` or `[]`
    *   Methods:
        *   `set_path(path: str) -> None`
        *   `set_root_path(root_path: str) -> None`
        *   `request_object(proxy_handle) -> StreamingHTTPRequest`

*   Implement `gRPCProxyRequest` class extending `ProxyRequest`:
    *   Constructor: `gRPCProxyRequest(request_proto: Any, context: grpc._cython.cygrpc._ServicerContext, service_method: str, stream: bool)`
    *   Properties:
        *   `request_type` returns `"grpc"`
        *   `method` returns `"GRPC"`
        *   `route_path` returns the application name from metadata
        *   `is_route_request` returns `True` if `service_method == "/ray.serve.RayServeAPIService/ListApplications"`
        *   `is_health_request` returns `True` if `service_method == "/ray.serve.RayServeAPIService/Healthz"`
        *   `user_request` returns the pickled request protobuf
        *   `app_name`, `request_id`, `multiplexed_model_id` extracted from metadata
        *   `method_name` is the lowercased last segment of `service_method`
    *   Methods:
        *   `send_request_id(request_id: str) -> None`
        *   `send_status_code(status_code: grpc.StatusCode) -> None`
        *   `send_details(message: str) -> None`
        *   `request_object(proxy_handle: ActorHandle) -> gRPCRequest`

*   Create `gRPCRequest` dataclass in `ray/serve/_private/common.py`:
    *   Fields:
        *   `grpc_user_request: bytes`
        *   `grpc_proxy_handle: ActorHandle`

*   Implement `ProxyResponse` class in `ray/serve/_private/proxy_request_response.py`:
    *   Constructor: `ProxyResponse(status_code: str, response: Optional[bytes] = None, streaming_response: Optional[Generator[bytes, None, None]] = None)`
    *   Properties:
        *   `status_code: str`
        *   `response: Optional[bytes]`
        *   `streaming_response: Optional[Generator[bytes, None, None]]`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.