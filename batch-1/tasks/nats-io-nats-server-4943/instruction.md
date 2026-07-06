Ensure the NATS server's OCSP monitoring restarts unconditionally during a configuration reload when gateway-specific TLS certificates are updated. Implement logic to re-establish all expected outbound gateway connections and maintain cross-gateway messaging functionality after certificate rotation.

*   Modify the NATS server's Reload method to restart OCSP monitoring:
    *   Ensure OCSP monitoring restarts not only when top-level TLS settings change but also when gateway-specific TLS certificates are updated.
*   Implement reconnection logic for gateway nodes:
    *   After a configuration reload with new gateway TLS certificates, ensure all nodes in the cluster re-establish the expected number of outbound gateway connections within a reasonable timeout.
*   Maintain cross-gateway message delivery:
    *   Ensure request/reply messaging between clients on different gateway nodes functions correctly after a gateway server reloads with new TLS certificates.
    *   Verify that cross-gateway requests complete successfully without timeout errors.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.