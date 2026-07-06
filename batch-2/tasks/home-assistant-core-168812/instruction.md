I've noticed a security gap in the Home Assistant WebSocket authentication: users that are designated as "local only" can still successfully authenticate through the WebSocket endpoint when connecting from a remote IP address outside my local network.

*   The WebSocket API authentication handler in homeassistant/components/websocket_api/auth.py must check whether a user marked as local-only is permitted to authenticate based on the IP address of the incoming request, before granting access.

*   When a local-only user submits a valid access token via WebSocket from a non-local (remote) IP address, authentication must fail with an auth-invalid response whose 'message' field contains exactly: 'User cannot authenticate remotely'.

*   When authentication is rejected due to the local-only IP restriction, the failed login attempt must be recorded by calling process_wrong_login (which is already imported in homeassistant/components/websocket_api/auth.py) with the current request.

*   When a local-only user submits a valid access token via WebSocket from a local IP address (such as a 192.168.x.x address), authentication must succeed and return an auth-ok response.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.