I'm hitting a bug in the pod CIDR allocation logic for dual-stack nodes.

*   When allocating both IPv4 and IPv6 pod CIDRs for a node and only one family's CIDR is already allocated by another node, the successfully claimed family must be occupied and kept — it must not be rolled back or released.

*   When only one IP family has an 'already allocated' conflict, the node must be stored in the manager's internal nodes map containing only the CIDR(s) from the successfully allocated family (the conflicting family is omitted).

*   When only one IP family has a conflict, the allocation function must return allocated=true (indicating a new occupation occurred) along with a non-nil error describing the conflict.

*   When both IPv4 and IPv6 families both conflict (both CIDRs are already allocated elsewhere), neither family must be occupied, the node must NOT be stored, and the return must be allocated=false with a non-nil error.

*   The error message returned when a CIDR is already allocated must contain the substring "already allocated".

*   After a partial conflict (one family kept, one stripped), the retained CIDR must remain marked as occupied in the allocator pool so that subsequent fresh allocations to other nodes do not reuse it.

*   When a node's spec is modified due to a partial conflict (a conflicting CIDR stripped from PodCIDRs), the pending Kubernetes operation for that node must be a full spec update (k8sOpUpdate), not a status-only update, so that the spec change is persisted.

*   After a partial conflict, the node's Spec.IPAM.PodCIDRs must be rebuilt to contain only the CIDRs for the successfully allocated family or families, omitting any conflicting CIDRs.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.