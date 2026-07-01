Implement a solution to ensure that certificate validity monitors restart when a server reloads its configuration with rotated gateway TLS certificates. This will maintain inter-cluster connectivity and message routing across the gateway mesh.

*   Ensure that the certificate validity monitors restart unconditionally when a server reloads its configuration with new gateway TLS certificates.
    *   This restart should occur regardless of whether TLS is configured at the root/client listener level.
*   Guarantee that after a server reload with rotated gateway TLS certificates, all gateway nodes in the mesh re-establish full outbound connectivity with each other.
    *   This connectivity should be achieved within a reasonable timeout.
*   Verify that after a server reload with rotated gateway TLS certificates, clients connected to any node in the gateway mesh can:
    *   Send requests to subjects subscribed on other nodes across the gateway.
    *   Receive replies successfully from those nodes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.