I'm working on Streamlit's server startup configuration.

*   The _get_uvicorn_config_kwargs function must return a dictionary containing exactly these keys: ssl_certfile, ssl_keyfile, ws, ws_ping_interval, ws_ping_timeout, ws_max_size, ws_per_message_deflate, use_colors, access_log — no more and no fewer.

*   The ws key in the returned dictionary must be 'auto' when the installed uvicorn version is semantically less than '0.44.0', including pre-release versions such as '0.44.0rc1' and '0.44.0.dev0'.

*   The ws key in the returned dictionary must be 'websockets-sansio' when the installed uvicorn version is '0.44.0' (final release) or any later version (e.g. '0.44.1', '0.45.0', '1.0.0').

*   The ssl_certfile value must reflect the server.sslCertFile config option and the ssl_keyfile value must reflect the server.sslKeyFile config option; both may be None when not configured.

*   The ws_ping_interval and ws_ping_timeout values must both reflect the server.websocketPingInterval config option; they must be equal to each other.

*   The ws_per_message_deflate value must reflect the server.enableWebsocketCompression config option (a boolean).

*   The use_colors and access_log values must always be False regardless of configuration.

*   The dictionary returned by _get_uvicorn_config_kwargs must be accepted by uvicorn.Config as keyword arguments without raising an error; the ws field in the resulting config object must be one of 'websockets-sansio' or 'auto'.


*   Interface details: Type: Function
Name: _get_uvicorn_config_kwargs
Location: lib/streamlit/web/server/starlette/starlette_server.py
Signature: _get_uvicorn_config_kwargs() -> dict[str, Any]
Description: Returns a dictionary of configuration keyword arguments for uvicorn. The returned dict must contain exactly these keys: ssl_certfile, ssl_keyfile, ws, ws_ping_interval, ws_ping_timeout, ws_max_size, ws_per_message_deflate, use_colors, access_log. The ws value must be "auto" when the installed uvicorn version is less than "0.44.0" (including pre-releases such as rc and dev variants), and "websockets-sansio" when the installed uvicorn version is "0.44.0" or higher (final release). The dict must be accepted by uvicorn.Config without raising.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.