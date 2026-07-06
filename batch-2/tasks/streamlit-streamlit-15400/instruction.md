I'm working on improving how the Streamlit server binds to network addresses at startup.

*   The _get_bind_address function must return '::' when the input address is '0.0.0.0', socket.has_ipv6 is True, and the server.address configuration option has NOT been manually set by the user.

*   The _get_bind_address function must return '0.0.0.0' unchanged when socket.has_ipv6 is False, regardless of whether the address was manually configured.

*   The _get_bind_address function must return the original address unchanged when the server.address configuration option has been manually set by the user (including '0.0.0.0' and specific IPs like '127.0.0.1').

*   The _bind_server_socket function must first attempt to bind using bind_address; if that raises an OSError with errno EAFNOSUPPORT, it must fall back to binding on server_address and return (socket, server_address).

*   The _bind_server_socket function must re-raise OSError with errno EADDRINUSE without attempting any fallback.

*   The _bind_server_socket function must pass port and backlog to _bind_socket in both the primary and fallback attempts.

*   The _UVICORN_STARTUP_FAILURE_EXIT_CODE constant must exist in the module and be used as the exit code when UvicornRunner detects that uvicorn failed to start.

*   The UvicornRunner.run() method must pre-bind a socket using _bind_socket (or _bind_server_socket for dual-stack logic), then create a uvicorn.Config object with app, host, and port, and then create a uvicorn.Server instance with that config.

*   The UvicornRunner.run() method must call uvicorn_instance.run(sockets=[socket]) passing the pre-bound socket.

*   The UvicornRunner.run() method must close the pre-bound socket after uvicorn finishes running.

*   The UvicornRunner.run() method must raise SystemExit with code _UVICORN_STARTUP_FAILURE_EXIT_CODE if the uvicorn server instance's started attribute is False after run() returns.

*   The UvicornRunner.run() method must update the uvicorn Config host to reflect the actual bound address when a fallback from '::' to '0.0.0.0' occurs due to EAFNOSUPPORT.

*   The UvicornRunner.run() method must retry with the next port when _bind_socket raises EADDRINUSE and the port was not manually set, consistent with existing retry behavior.

*   The bootstrap.run_asgi_app function must instantiate UvicornRunner with the app import string and call its run() method, rather than invoking the underlying server's run function directly.

*   When the server's default address is not configured and IPv6 is available, _bind_socket must be called with '::' instead of '0.0.0.0'.


*   Interface details: Type: Function
Name: _get_bind_address
Location: lib/streamlit/web/server/starlette/starlette_server.py
Signature: _get_bind_address(address: str) -> str
Description: Determines the actual bind address to use. When address is "0.0.0.0", socket.has_ipv6 is True, and the server.address config option is NOT manually set, returns "::". Otherwise returns the original address unchanged. This allows automatic IPv6 dual-stack binding on capable systems while respecting explicit user configuration.

Type: Function
Name: _bind_server_socket
Location: lib/streamlit/web/server/starlette/starlette_server.py
Signature: _bind_server_socket(server_address: str, bind_address: str, port: int, backlog: int) -> tuple[socket.socket, str]
Description: Attempts to bind a server socket to bind_address first. If that raises OSError with errno EAFNOSUPPORT (address family not supported), falls back to binding on server_address instead, returning (socket, server_address). If the error is EADDRINUSE, the error is re-raised without fallback. On success, returns (socket, actual_bind_address).

Type: Constant
Name: _UVICORN_STARTUP_FAILURE_EXIT_CODE
Location: lib/streamlit/web/server/starlette/starlette_server.py
Description: Integer exit code used when the uvicorn server fails to start (i.e., uvicorn_instance.started is False after run completes). Passed to SystemExit when this condition is detected.

Type: Class
Name: UvicornRunner
Location: lib/streamlit/web/server/starlette/starlette_server.py
Description: Synchronous blocking runner for st.App mode. Modified run() method to pre-bind a socket via _bind_socket/_bind_server_socket, then create a uvicorn.Config and uvicorn.Server instance, call uvicorn_instance.run(sockets=[socket]), and close the socket afterward. If uvicorn_instance.started is False after run, calls sys.exit(_UVICORN_STARTUP_FAILURE_EXIT_CODE). The uvicorn Config host reflects the actual bound address including any IPv6-to-IPv4 fallback.
Signature: __init__(self, app: str) -> None; run(self) -> None


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.