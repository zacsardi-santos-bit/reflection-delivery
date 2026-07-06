Update the OpenTelemetry Go SDK to use the latest semantic conventions version and modify the Zipkin exporter to correctly extract remote endpoints using updated attribute names and priority ordering. Ensure that all references to outdated attribute names are replaced with their current equivalents.

*   Update the OpenTelemetry semantic conventions package to version 1.24.0.
*   Replace all references to the HTTP response status code attribute:
    *   Use 'http.response.status_code' instead of 'http.status_code'.
*   Update network peer address and port attributes:
    *   Use 'network.peer.address' instead of 'net.sock.peer.addr'.
    *   Use 'network.peer.port' instead of 'net.sock.peer.port'.
*   Modify the Zipkin exporter to extract remote endpoints with the following priority order:
    *   peer.service → ServiceName
    *   server.address → ServiceName
    *   net.peer.name → ServiceName
    *   network.peer.address → IP (use port from 'network.peer.port')
    *   server.socket.domain → ServiceName
    *   server.socket.address → IP (use port from 'server.socket.port')
    *   net.sock.peer.name → ServiceName
    *   net.sock.peer.addr → IP (use port from 'net.sock.peer.port')
    *   peer.hostname → ServiceName
    *   peer.address → IP
    *   db.name → ServiceName
*   Ensure correct port selection for IP-based endpoint resolution:
    *   Use 'network.peer.port' with 'network.peer.address'.
    *   Use 'server.socket.port' with 'server.socket.address'.
    *   Use 'net.sock.peer.port' with 'net.sock.peer.addr'.
*   Handle invalid IP values:
    *   If 'network.peer.address' is not a valid IP, set the remote endpoint to nil.
*   Correctly parse IPv6 addresses:
    *   Store IPv6 addresses from 'network.peer.address' in the Zipkin endpoint's IPv6 field, even if no port is present.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.