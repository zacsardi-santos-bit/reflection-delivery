Implement enhancements to the cable path tracing feature in NetBox to address connectivity issues involving circuits and multiple patch panels. Ensure that the system correctly resolves end-to-end connectivity in various topologies and handles deletions appropriately.

*   Update the cable path tracing logic to:
    *   Recognize circuits as pass-throughs when cables connect device interfaces to a circuit's A-side and Z-side terminations.
    *   Set each interface's `connected_endpoint` to the other interface and `connection_status` to True when connected through a circuit.
    *   Clear `connected_endpoint` and `connection_status` on both interfaces when the circuit is deleted.

*   Enhance path resolution through patch panels:
    *   Ensure paths through more than two consecutive patch panels resolve correctly, including mixed topologies of rear-port-to-rear-port and front-port-to-rear-port segments.
    *   Set `connected_endpoint` and `connection_status` correctly on endpoint interfaces when a complete path is formed through multiple patch panels.
    *   Reset `connected_endpoint` and `connection_status` to None on both endpoint interfaces when any cable in the path is deleted.

*   Support mixed topologies:
    *   Ensure paths combining patch panels and circuits resolve correctly, setting `connected_endpoint` and `connection_status` on both endpoint interfaces.
    *   Nullify `connected_endpoint` and `connection_status` on both interfaces when the circuit in a mixed topology is deleted.

*   Adjust cable validation rules:
    *   Allow connections between a `RearPort` and a `CircuitTermination` without validation errors.
    *   Apply position count equality checks only when both terminations are `RearPorts`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.